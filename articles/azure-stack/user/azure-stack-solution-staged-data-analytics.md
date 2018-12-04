---
title: Utwórz rozwiązanie analizy użycia przemieszczonych danych dzięki platformie Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Informacje dotyczące tworzenia rozwiązania analizy użycia przemieszczonych danych dzięki platformie Azure i usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: b043c5ebe4c2a02bd4d40ca4b2bb7d5f488f5747
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837345"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Samouczek: Tworzenie rozwiązania analizy użycia przemieszczonych danych dzięki platformie Azure i usługi Azure Stack 

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak korzystać zarówno lokalnie, jak i środowisk chmury publicznej do zmierzenia się z wielu funkcji przedsiębiorstwa. Usługa Azure Stack oferuje szybkie, bezpieczne i elastyczne rozwiązanie w zakresie zbierania, przetwarzania, przechowywania i rozpowszechniania danych lokalnych i zdalnych, szczególnie w przypadku, gdy bezpieczeństwa, poufności, zasady firmowe i wymogów prawnych mogą się różnić między lokalizacjami i użytkowników.

W tym wzorcu klientów są zbierane dane, które wymaga analizy punkcie kolekcji, tak, aby umożliwić szybkie decyzje. Występuje często, zbierania danych z Brak dostępu do Internetu. Gdy połączenie zostanie ustanowione, może być konieczne czy analizy danych, aby uzyskać dodatkowe szczegółowe informacje o dużej ilości zasobów. Nadal możesz przeanalizować dane, gdy chmura publiczna jest za późno lub niedostępny.

W tym samouczku należy utworzyć środowisko próbki do:

> [!div class="checklist"]
> - Utwórz obiekt blob magazynu danych pierwotnych.
> - Tworzenie nowej funkcji stosu platformy Azure, aby przenieść Wyczyść dane z usługi Azure Stack na platformie Azure.
> - Tworzenie funkcji wyzwalanej przez magazyn obiektów Blob.
> - Tworzenie konta magazynu usługi Azure Stack, zawierający obiekt blob i kolejki.
> - Tworzenie funkcji wyzwalanej przez kolejkę.
> - Funkcja wyzwalana przez test kolejki.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi aplikacje hybrydowe. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

Pewne przygotowania jest wymagane do skompilowania tego rozwiązania:

-   Azure Stack zainstalowany i działa (więcej informacji można znaleźć tutaj: [Omówienie usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Subskrypcja platformy Azure. (Tworzenie [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Pobrać i zainstalować program [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

-   Należy podać dane do przetworzenia przez funkcje. Dane muszą być generowane i dostępne do przekazania do kontenera obiektów blob magazynu Azure Stack.

## <a name="issues-and-considerations"></a>Problemy i zagadnienia

### <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Usługa Azure functions i pamięci masowej skalowane w celu spełnienia ilości danych i przetwarzanie żądania. Informacje dotyczące skalowalności platformy Azure i elementy docelowe, zobacz [dokumentacji platformy Azure skalowalność](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Magazyn to kwestia dostępności podstawowego dla tego wzoru. Połączenie za pośrednictwem szybkich łączach jest wymagany do przetwarzania woluminu dużych ilości danych i dystrybucji.

### <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Należy wziąć pod uwagę, jak Twoje narzędzia programistyczne i kontrola źródła umożliwi zarządzania rozwiązaniem.

## <a name="create-the-raw-data-storage-blob"></a>Utwórz obiekt blob magazynu danych pierwotnych

Kontener konta i obiektów blob magazynu będzie przechowywać wszystkie oryginalne dane wygenerowane przez działania w środowisku lokalnym, w tym działanie maszyny i pracowników, funkcji danych, metryki produkcji i innych raportowania.

1.  Zaloguj się do [ *portalu Azure Stack*](https://portal.local.azurestack.external/).

2.  W portalu usługi Azure Stack rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz **wszystkich usług**. Przewiń w dół do **magazynu** i wybierz polecenie **kont magazynu**. W oknie konta magazynu wybierz **Dodaj**.

3.  Dla konta, skorzystaj z poniższych informacji:

    a.  Nazwa: **wybór**

    b.  Model wdrażania: **usługi Resource Manager**

    c.  Rodzaj konta: **magazynu (ogólnego przeznaczenia w wersji 1)**

    d.  Lokalizacja: **Zachodnie stany USA**

    e.  Replikacja: **magazyn lokalnie nadmiarowy (LRS)**

    f.  Wydajność: **standardowe**

    g.  Wymagany bezpieczny transfer: **wyłączone**

    h.  Subskrypcja: Wybierz jeden

    i.  Grupa zasobów: Określ nową grupę zasobów lub wybierz istniejącą grupę zasobów

    j.  Konfigurowanie sieci wirtualnych: **wyłączone**

4.  Wybierz **Utwórz, aby utworzyć konto magazynu**.

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image1.png)

5.  Po utworzeniu, wybierz nazwę konta magazynu.

6.  W bloku konta w ramach **usługę BLOB SERVICE** nagłówka, wybierz **kontenery**.

7.  W górnej części bloku wybierz **+ kontener.** i wybierz **kontenera**.

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image2.png)

8.  Nazwa: **wybór**

9.  Poziom dostępu publicznego: **kontenera** (anonimowy dostęp do odczytu dla kontenerów i obiektów blob.)

10.  Kliknij przycisk **OK**.

## <a name="create-an-azure-stack-function"></a>Tworzenie funkcji usługi Azure Stack

Tworzenie nowej funkcji usługi Azure Stack, aby przenieść Wyczyść dane z usługi Azure Stack na platformie Azure.

### <a name="create-the-azure-stack-function-app"></a>Tworzenie aplikacji funkcji usługi Azure Stack

1. Zaloguj się do [portalu Azure Stack](https://portal.local.azurestack.external).
2. Wybierz pozycję **Wszystkie usługi**.
3. Wybierz **aplikacje funkcji** w **sieci Web i mobilność** grupy.

4.  Tworzenie aplikacji funkcji przy użyciu ustawień określonych w tabeli znajdującej się poniżej obrazu.

    | Ustawienie | Sugerowana wartość | Opis |
    | ---- | ---- | ---- |
    | Nazwa aplikacji | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a` - `z`, `0``-9`, i `-`. |
    | Subskrypcja | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **Grupa zasobów** |  |  |
    | myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |  |
    | System operacyjny | Windows | Hosting bezserwerowy jest obecnie dostępny tylko w przypadku uruchamiania w systemie Windows. |
    | **Plan hostingu** |  |  |
    | Plan Zużycie | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym Plan zużycie zasobów są dodawane dynamicznie zgodnie z wymaganiami funkcji. W tym hosting bezserwerowy, zapłacisz tylko za czas, gdy funkcje są uruchomione. |  |
    | Lokalizacja | Region najbliższą | Wybierz region okolicy lub w pobliżu innych usług dostępu do usługi functions. |
    | **Konto magazynu** |  |  |
    | \<konta magazynu utworzonego powyżej > | Nazwa nowego konta magazynu używanego przez aplikację funkcji. Nazwy kont magazynu muszą być od 3 do 24 znaków. Nazwa może składać się tylko cyfry i małe litery. Możesz także użyć istniejącego konta. |  |

    **Przykład:**

    ![Definiowanie nowych ustawień aplikacji funkcji](media\azure-stack-solution-staged-data-analytics\image6.png)

5.  Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

6.  Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](media\azure-stack-solution-staged-data-analytics\image7.png)

7.  Wybierz **przejdź do zasobu** Aby wyświetlić nową aplikację funkcji.

![Pomyślnie utworzona aplikacja funkcji.](media\azure-stack-solution-staged-data-analytics\image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Dodawanie funkcji do aplikacji funkcji usługi Azure Stack

1.  Utwórz nową funkcję, klikając **funkcje**, a następnie **+ nowa funkcja** przycisku.

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image3.png)

2.  Wybierz **wyzwalacza czasomierza**.

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image4.png)

3.  Wybierz **C\#**  jako język i nazwa funkcji: `upload-to-azure` Ustaw harmonogram `0 0 * * * *`, ponieważ w CRON jest raz godzinę.

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Tworzenie funkcji wyzwalanej przez magazyn obiektów Blob

1.  Rozwiń aplikację funkcji, a następnie wybierz pozycję **+** znajdujący się obok **funkcji**.

2.  W polu wyszukiwania wpisz `blob` , a następnie wybierz żądany język dla **obiekt Blob wyzwalacza** szablonu.

  ![Wybierz szablon wyzwalacza usługi Blob Storage.](media\azure-stack-solution-staged-data-analytics\image10.png)

3.  Użyj ustawień określonych w tabeli poniżej:

    | Ustawienie | Sugerowana wartość | Opis |
    | ------- | ------- | ------- |
    | Name (Nazwa) | Unikatowa w obrębie aplikacji funkcji | Nazwa funkcji wyzwalanej przez obiekt blob. |
    | Ścieżka | \<Ścieżka z powyższych lokalizacji magazynu > | Lokalizacja w monitorowanym magazynie obiektów Blob. Nazwa pliku obiektu blob jest przekazywana w powiązaniu jako parametr name. |
    | Połączenie konta magazynu | Połączenie aplikacji — funkcja | Można użyć połączenie konta magazynu, które są już używane przez aplikację funkcji lub utworzyć nową. |

    **Przykład:**

    ![Tworzenie funkcji wyzwalanej przez magazyn obiektów Blob.](media\azure-stack-solution-staged-data-analytics\image11.png)

4.  Wybierz **Utwórz** Aby utworzyć funkcję.

### <a name="test-the-function"></a>Testowanie funkcji

1.  W witrynie Azure portal przejdź do funkcji. Rozwiń **dzienniki** w dolnej części strony i upewnij się, strumieniowe przesyłanie dzienników nie została wstrzymana.

2.  Otwórz Eksploratora usługi Storage, a następnie nawiązać połączenie z kontem magazynu utworzonym na początku tej sekcji.

3.  Rozwiń konto magazynu, **kontenerach obiektów Blob**, oraz obiekt blob utworzone wcześniej. Wybierz **przekazywanie** i następnie **przekazywanie plików**.

    ![Przekazywanie pliku do kontenera obiektów Blob.](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  W oknie dialogowym przekazywanie plików wybierz pole plików. Przejdź do pliku na komputerze lokalnym, na przykład pliku obrazu, zaznacz ją i wybierz **Otwórz** i następnie **przekazywanie**.

5.  Wróć do dzienników funkcji i sprawdź, czy obiekt blob został odczytany.

    **Przykład:**

    ![Wyświetlanie komunikatu w dziennikach.](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Tworzenie konta magazynu usługi Azure Stack

Tworzenie konta magazynu usługi Azure Stack, zawierający obiekt blob i kolejki.

### <a name="storage-blob--data-archiving"></a>Archiwizowanie danych obiektu Blob magazynu

To konto magazynu będzie przechowywać dwóch kontenerów. Te kontenery są jeden obiekt blob używany do przechowywania danych archiwum i kolejki używana na potrzeby przetwarzania danych przeznaczone do dystrybucji w głównej siedzibie firmy.

Skorzystaj z kroków i ustawień opisanych powyżej, można utworzyć innego konta i obiektów blob kontenera magazynu jako naszej usługi archive storage.

### <a name="storage-queue--filtered-data-holding"></a>Przechowywania danych filtrowane kolejki magazynu

1.  Skorzystaj z kroków i ustawień opisanych powyżej, aby uzyskać dostęp do nowego konta magazynu.

2.  W sekcji omówienia kont magazynu wybierz **kolejki.**

3.  Wybierz **+ kolejka** i wypełnianie **nazwa** pola i wypełnij nazwę nowej kolejki.

4.  Wybierz **OK.**

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image14.png)

    ![Tekst alternatywny](media\azure-stack-solution-staged-data-analytics\image15.png)

## <a name="create-a-queue-triggered-function"></a>Tworzenie funkcji wyzwalanej przez kolejkę

1.  Wykonaj kroki w powyższej sekcji Tworzenie funkcji tworzenia dodatkowych funkcji usługi Azure Stack z wyzwalaczem kolejki zamiast wyzwalacz obiektu blob.

2.  Użyj ustawień określonych w tabeli poniżej:

    | Ustawienie | Sugerowana wartość | Opis |
    | ------- | ------- | ------- |
    | Name (Nazwa) | Unikatowa w obrębie aplikacji funkcji | Nazwa funkcji wyzwalanej przez kolejkę. |
    | Ścieżka | \<Ścieżka z powyższych lokalizacji magazynu > | Lokalizacja w monitorowanym magazynie. Nazwa pliku kolejki jest przekazywana w powiązaniu jako parametr name. |
    | Połączenie konta magazynu | Połączenie aplikacji — funkcja | Można użyć połączenie konta magazynu, które są już używane przez aplikację funkcji lub utworzyć nową. |

3.  Wybierz **Utwórz** Aby utworzyć funkcję.

## <a name="test-the-queue-triggered-function"></a>Funkcja wyzwalana przez test kolejki

1.  W portalu usługi Azure Stack przejdź do funkcji. Rozwiń **dzienniki** w dolnej części strony i upewnij się, strumieniowe przesyłanie dzienników nie została wstrzymana.

2.  Otwórz Eksploratora usługi Storage, a następnie nawiązać połączenie z kontem magazynu utworzonym na początku tej sekcji.

3.  Rozwiń konto magazynu, **kontenerach obiektów Blob**, oraz obiekt blob utworzone wcześniej. Wybierz **przekazywanie** i następnie **przekazywania plików.**

    ![Przekazywanie pliku do kontenera obiektów Blob.](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  W oknie dialogowym przekazywanie plików wybierz pole plików. Przejdź do pliku na komputerze lokalnym, na przykład pliku obrazu, zaznacz ją i wybierz **Otwórz** i następnie **przekazywanie**.

5.  Wróć do dzienników funkcji i sprawdź, czy obiekt blob został odczytany.

  **Przykład:**

    ![Wyświetlanie komunikatu w dziennikach.](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Bezpiecznie przechowywane i dostęp do danych zgodne z

Danych w przedsiębiorstwie globalnym jest bezpiecznie przechowywane, przetwarzane, rozproszone i dostępne za pośrednictwem platformy Azure i usługi Azure Stack przygotowane Data Analytics i dyrektyw niestandardowego punktu końcowego. Działania pracowników i maszyny odległego biura, funkcji danych i metryki biznesowe są stale skompilowany, przechowywane, przetestowana pod kątem zgodności i dystrybuowane zgodnie z zasadami firmy i regionalne rozporządzenia.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).