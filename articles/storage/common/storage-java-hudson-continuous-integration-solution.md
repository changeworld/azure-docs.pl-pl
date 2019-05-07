---
title: Jak używać rozwiązania Hudson z magazynu obiektów Blob | Dokumentacja firmy Microsoft
description: Opisuje sposób używania rozwiązania Hudson za pomocą usługi Azure Blob storage jako repozytorium artefaktów kompilacji.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 4b47af857fada453e36fcb0c23e6d89e5ad90e42
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154338"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem ciągłej integracji Hudson
## <a name="overview"></a>Omówienie
Poniżej pokazano, jak używać magazynu obiektów Blob jako repozytorium artefaktów kompilacji utworzonego przez rozwiązanie Hudson ciągłej integracji (CI) lub jako źródło plików do pobrania ma być używany w procesie kompilacji. Jednego ze scenariuszy, w którym użytkownik może to być przydatne jest one kodowania w środowisku zwinne Wytwarzanie oprogramowania (przy użyciu platformy Java lub innych języków), wykonywane są kompilacje zależności o ciągłej integracji, gdy potrzebujesz repozytorium artefaktów kompilacji, dzięki czemu można wykonać następujące akcje, na przykład udostępniać je innym członkom organizacji, klientów lub Obsługa archiwum.  Inny scenariusz polega na podczas samego zadania kompilacji wymaga innych plików, na przykład, zależności, aby pobrać jako część kompilacji danych wejściowych.

W ramach tego samouczka używana będzie wtyczka usługi Azure Storage dla ciągłej integracji Hudson udostępnianych przez firmę Microsoft.

## <a name="introduction-to-hudson"></a>Wprowadzenie do rozwiązania Hudson
Hudson umożliwia ciągłą integrację z projektem oprogramowania, umożliwiając deweloperom łatwe integrowanie ich zmian w kodzie i mieć kompilacje generowane automatycznie i często, a tym samym zwiększenia produktywności deweloperów. Kompilacje są wersjonowane i artefaktów kompilacji można przekazać do różnych repozytoriów. W tym artykule pokazują, jak używać usługi Azure Blob storage jako repozytorium artefaktów kompilacji. Widoczna będzie również sposób pobierania zależności z usługi Azure Blob storage.

Więcej informacji na temat rozwiązania Hudson znajduje się w temacie [spełniają Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Korzyści z używania usługi obiektów Blob
Korzyści z używania usługi obiektów Blob, aby hostować swoje artefakty kompilacji zwinne Wytwarzanie oprogramowania obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub zależności do pobrania.
* Wydajność, gdy rozwiązanie ciągłej integracji Hudson przekazuje swoje artefakty kompilacji.
* Wydajność, gdy klienci i partnerzy pobierają swoje artefakty kompilacji.
* Kontrolę nad zasad dostępu użytkownika, z możliwością wyboru między dostępu anonimowego dostępu sygnatury dostępu współdzielonego opartych na wygaśnięcie, prywatne, access, itp.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące polecenie, aby korzystać z usługi obiektów Blob z rozwiązaniem ciągłej integracji Hudson będą potrzebne:

* Rozwiązanie Hudson Continuous Integration.
  
    Obecnie nie masz rozwiązanie ciągłej integracji Hudson, po uruchomieniu rozwiązania ciągłej integracji Hudson za pomocą następujących technik:
  
  1. Na maszynie z obsługą języka Java, Pobierz WAR Hudson z <http://hudson-ci.org/>.
  2. W wierszu polecenia, który jest otwierany do folderu, który zawiera Hudson WAR Uruchom Hudson WAR. Na przykład, jeśli pobrano program w wersji 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. Otwórz w przeglądarce, `http://localhost:8080/`. Spowoduje to otwarcie pulpitu nawigacyjnego rozwiązania Hudson.
  4. Przy pierwszym użyciu Hudson przeprowadzenia konfiguracji początkowej w `http://localhost:8080/`.
  5. Po ukończenia początkowej konfiguracji, Anuluj uruchomione wystąpienie Hudson WAR, ponownie uruchom Hudson WAR i ponownie otwórz pulpit nawigacyjny rozwiązania Hudson, `http://localhost:8080/`, który będzie używany do instalowania i konfigurowania wtyczki usługi Azure Storage.
     
      Podczas gdy typowe rozwiązanie z ciągłą Integracją rozwiązania Hudson, należy skonfigurować do uruchamiania jako usługa, uruchomiona Hudson war w wierszu polecenia, będzie wystarczająca na potrzeby tego samouczka.
* Konto platformy Azure. Możesz zasubskrybować konta platformy Azure w <https://www.azure.com>.
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz utworzyć ją przy użyciu czynności opisane w temacie [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).
* Znajomość rozwiązanie ciągłej integracji Hudson jest zalecane, ale nie jest to wymagane, zgodnie z następującą zawartością użyje podstawowy przykład aby pokazać, że kroki wymagane w przypadku korzystania z usługi Blob service jako repozytorium do ciągłej integracji Hudson artefaktów kompilacji.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Jak używać usługi obiektów Blob z ciągłą Integracją rozwiązania Hudson
Usługi obiektów Blob za pomocą rozwiązania Hudson, musisz zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do używania konta magazynu, a następnie utwórz akcji po kompilacji, która przekazuje swoje artefakty kompilacji do swojego konta magazynu. Te kroki opisano w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Zarządzanie Hudson**.
2. Na **Zarządzanie Hudson** kliknij **Zarządzaj wtyczkami**.
3. Kliknij przycisk **dostępne** kartę.
4. Kliknij przycisk **innych**.
5. W **osób przekazujących artefaktu** zaznacz **dodatek usługi Microsoft Azure Storage**.
6. Kliknij pozycję **Zainstaluj**.
7. Po zakończeniu instalacji uruchom ponownie Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurowanie wtyczki usługi Azure Storage do używania konta magazynu
1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Zarządzanie Hudson**.
2. Na **Zarządzanie Hudson** kliknij **Konfiguruj System**.
3. W **konfigurację konta magazynu Azure Microsoft** sekcji:
   
    a. Wprowadź nazwę konta magazynu, którego można uzyskać z [witryny Azure Portal](https://portal.azure.com).
   
    b. Wprowadź klucz konta magazynu, również u: [witryny Azure Portal](https://portal.azure.com).
   
    c. Użyj wartości domyślnej dla **adres URL punktu końcowego usługi Blob** korzystania z chmury publicznej platformy Azure. Jeśli używasz innej chmurze platformy Azure, używać punktu końcowego, jak to określono w [witryny Azure Portal](https://portal.azure.com) konta magazynu.
   
    d. Kliknij przycisk **przeprowadzenie walidacji poświadczeń magazynu** do zweryfikowania konta magazynu.
   
    e. [Opcjonalnie] Jeśli masz dodatkowe konta magazynu, które mają zostać udostępnione do ciągłej integracji Hudson, kliknij przycisk **dodać więcej kont magazynu**.
   
    f. Kliknij przycisk **Zapisz** można zapisać ustawień.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcji wykonywanych po kompilacji, która przekazuje swoje artefakty kompilacji do swojego konta magazynu
Do celów instrukcji najpierw musimy utworzyć zadanie, które będzie utworzyć kilka plików, a następnie dodaj w działaniu po kompilacji, aby przekazać pliki do swojego konta magazynu.

1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **nowe zadanie**.
2. Nadaj nazwę zadaniu **MyJob**, kliknij przycisk **kompilacji zadanie oprogramowania stylu**, a następnie kliknij przycisk **OK**.
3. W **kompilacji** sekcji konfiguracji zadania kliknij **Dodaj krok kompilacji** i wybierz polecenie **Windows wykonania polecenia batch**.
4. W **polecenia**, użyj następujących poleceń:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. W **akcjach wykonywanych po kompilacji** sekcji konfiguracji zadania kliknij **Przekaż artefakty do usługi Microsoft Azure Blob storage**.
6. Aby uzyskać **nazwa konta magazynu**, wybierz konto magazynu do użycia.
7. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera. (Zostanie utworzony kontener Jeśli go jeszcze nie istnieje przekazywane artefaktów kompilacji.) Możesz użyć zmiennych środowiskowych, dlatego w tym przykładzie należy wprowadzić **${parametr JOB_NAME}** jako nazwę kontenera.
   
    **Porada**
   
    Poniżej **polecenia** sekcji, w którym wprowadzono skryptu dla **Windows wykonania polecenia batch** łącze do zmiennych środowiskowych rozpoznawane przez Hudson. Kliknij ten link, aby dowiedzieć się, nazwy zmiennych środowiskowych i opisy. Należy pamiętać, że zmienne środowiskowe, które zawierają specjalne znaków, takich jak **BUILD_URL** zmiennej środowiskowej, nie są dozwolone jako nazwa kontenera lub wspólnej ścieżki wirtualnej.
8. Kliknij przycisk **upublicznić domyślnie nowy kontener** w tym przykładzie. (Jeśli chcesz użyć kontenera prywatnych, należy utworzyć sygnaturę dostępu współdzielonego, aby zezwolić na dostęp. Który wykracza poza zakres tego artykułu. Dowiedz się więcej na temat sygnatur dostępu współdzielonego w [za pomocą sygnatur dostępu udostępnionego (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcjonalnie] Kliknij przycisk **czyste kontenera przed przekazaniem** Jeśli chcesz, aby kontener do wyczyszczenia zawartości przed artefakty kompilacji są przekazywane (pozostaw to pole niezaznaczone, jeśli nie chcesz wyczyścić zawartość kontenerów).
10. Aby uzyskać **listy artefakty do przekazania**, wprowadź **tekst/*.txt**.
11. Dla **wspólnej ścieżki wirtualnej dla przekazanych artefaktów**, wprowadź **${kompilacji\_identyfikator} / ${kompilacji\_numer}**.
12. Kliknij przycisk **Zapisz** można zapisać ustawień.
13. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Kompiluj teraz** do uruchomienia **MyJob**. Sprawdź dane wyjściowe konsoli z stanu. Komunikaty o stanie dla usługi Azure Storage będą uwzględniane w danych wyjściowych konsoli, po uruchomieniu akcji wykonywanych po kompilacji, można przekazać artefaktów kompilacji.
14. Po pomyślnym zakończeniu zadania można sprawdzić artefaktów kompilacji, otwierając publicznego obiektu blob.
    
    a. Zaloguj się do [Portalu Azure](https://portal.azure.com).
    
    b. Kliknij przycisk **magazynu**.
    
    c. Kliknij nazwę konta magazynu, które jest używane jako Hudson.
    
    d. Kliknij przycisk **kontenery**.
    
    e. Kliknij kontener o nazwie **myjob**, czyli małe wersję nazwę zadania, przypisana podczas tworzenia zadania Hudson. Nazwy kontenerów i obiektów blob są małe litery (i wielkość liter) w usłudze Azure Storage. W obrębie listy obiektów blob w kontenerze o nazwie **myjob** powinien zostać wyświetlony **hello.txt** i **date.txt**. Skopiuj adres URL dla dowolnego z tych elementów, a następnie otwórz go w przeglądarce. Plik tekstowy, który został przekazany zostanie wyświetlona jako artefakt kompilacji.

Poszczególne zadania można utworzyć tylko jedną akcje po kompilacji, która przekazuje artefakty do usługi Azure Blob storage. Należy pamiętać, że jednej akcji wykonywanych po kompilacji do przekazania artefakty do usługi Azure Blob storage można określić różne pliki (łącznie z symbolami wieloznacznymi) i ścieżki do plików w ramach **listy artefakty do przekazania** przy użyciu średnika jako separatora. Na przykład jeśli Twoje rozwiązania Hudson kompilacji tworzy pliki JAR i pliki TXT w Twoim obszarze roboczym **kompilacji** folderu i chcesz przekazać zarówno do usługi Azure Blob storage, należy użyć następującego dla **listy artefakty do przekazania** wartość: **kompilacji /\*JAR; kompilacja /\*.txt**. Składnia dwoma dwukropkami umożliwia również określić ścieżkę do użycia w nazwie obiektu blob. Na przykład, jeśli chcesz, aby plikach JAR, aby zostać przekazane za pomocą **pliki binarne** ścieżka obiektu blob i plików TXT, aby zostać przekazane za pomocą **uwagi** w ścieżce obiektu blob, należy użyć następującego dla **listę artefaktów Aby przekazać** wartość: **kompilacji /\*. jar::binaries; kompilacja /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji, który pobiera z usługi Azure Blob storage
Poniższe kroki pokazują sposób konfigurowania kroku kompilacji można pobrać elementów z usługi Azure Blob storage. Jest to przydatne, jeśli chcesz dołączyć elementy do kompilacji, na przykład plikach JAR, znajdujących się w usłudze Azure Blob storage.

1. W **kompilacji** sekcji konfiguracji zadania kliknij **Dodaj krok kompilacji** i wybierz polecenie **pobierać z usługi Azure Blob storage**.
2. Aby uzyskać **nazwa konta magazynu**, wybierz konto magazynu do użycia.
3. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera, który zawiera obiekty BLOB, którą chcesz pobrać. Można używać zmiennych środowiskowych.
4. Aby uzyskać **nazwa obiektu Blob**, określ nazwę obiektu blob. Można używać zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu znakami nazwy obiektu blob. Na przykład **projektu\\*** należałoby określić wszystkie obiekty BLOB, których nazwy rozpoczynają się od **projektu**.
5. [Opcjonalnie] Aby uzyskać **ścieżkę pobierania**, określ ścieżkę na maszynie Hudson, które chcesz pobrać pliki z usługi Azure Blob storage. Można także zmienne środowiskowe. (Jeśli nie zostanie określona wartość **ścieżkę pobierania**, zostaną pobrane pliki z usługi Azure Blob storage z obszarem roboczym zadania.)

Jeśli masz dodatkowe elementy, które chcesz pobrać z usługi Azure Blob storage, możesz utworzyć dodatkowych kroków kompilacji.

Po uruchomieniu kompilacji, można sprawdzić dane wyjściowe konsoli z kompilacji historii lub Przyjrzyj się do lokalizacji pobierania, aby zobaczyć, czy obiekty BLOB, które miały zostały pomyślnie pobrane.

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez usługę obiektów Blob
Poniżej zawiera omówienie składników usługi obiektów Blob.

* **Konto magazynu**: Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Jest to najwyższy poziom przestrzeni nazw do uzyskiwania dostępu do obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, tak długo, jak ich łączny rozmiar wynosi poniżej 100 TB.
* **kontener**: Kontener zawiera grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt blob**: Plik dowolnego typu i rozmiaru. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blokowe i stronicowe obiekty BLOB. Większość plików to blokowe obiekty BLOB. Pojedynczy blokowy obiekt blob może być maksymalnie 200 GB. W tym samouczku korzysta z blokowych obiektów blob. Stronicowe obiekty BLOB, innego typu obiektu blob, może być maksymalnie 1 TB, rozmiar i są bardziej efektywne, gdy zakresów bajtów w pliku są często modyfikowane. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [omówienie blokowych obiektów blob, Uzupełnialnych obiektów blob i stronicowe obiekty BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL**: Obiekty BLOB są adresy, przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Format powyżej ma zastosowanie do chmury publicznej platformy Azure. Jeśli używasz innej chmurze platformy Azure, użyj punktu końcowego w ramach [witryny Azure Portal](https://portal.azure.com) Aby określić adres URL punktu końcowego.)
  
    W powyższym formacie `storageaccount` reprezentuje nazwę konta magazynu `container_name` reprezentuje nazwę kontenera, i `blob_name` reprezentuje nazwę obiektu blob, odpowiednio. W ramach nazwa kontenera może mieć wiele ścieżek oddzielonych ukośnikiem, **/**. Przykładowa nazwa kontenera, w tym samouczku został **MyJob**, i **${kompilacji\_identyfikator} / ${kompilacji\_numer}** został użyty podczas wspólnej ścieżki wirtualnej skutkuje o adresie URL obiektu blob następującą postać:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Kolejne kroki
* [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Usługa Azure Storage SDK dla języka Java](https://github.com/azure/azure-storage-java)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).
