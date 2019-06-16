---
title: Za pomocą usługi Azure Storage z rozwiązaniem ciągłej integracji Jenkins
description: W tym samouczku pokazano, jak używać usługi Azure blob service jako repozytorium na potrzeby kompilacji artefaktów utworzonego przez rozwiązanie ciągłej integracji narzędzia Jenkins.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 07/31/2018
ms.subservice: common
ms.openlocfilehash: d9ef6f5056fdbd7187c92c98d1c884a5314c29a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153670"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Za pomocą usługi Azure Storage z rozwiązaniem ciągłej integracji Jenkins

W tym artykule pokazano, jak używać magazynu obiektów Blob jako repozytorium artefaktów kompilacji utworzonego przez rozwiązanie ciągłej integracji (CI) Jenkins lub jako źródło plików do pobrania ma być używany w procesie kompilacji. Jednego ze scenariuszy, w którym będzie okaże się to rozwiązanie przydatne jest, gdy masz kodowania w środowisku zwinne Wytwarzanie oprogramowania (przy użyciu platformy Java lub innych języków), wykonywane są kompilacje zależności o ciągłej integracji i potrzebujesz repozytorium artefaktów kompilacji, tak aby można na przykład, udostępniać je innym członkom organizacji, klientów lub Obsługa archiwum. Inny scenariusz polega na podczas samego zadania kompilacji wymaga innych plików, na przykład, zależności, aby pobrać jako część kompilacji danych wejściowych.

W tym samouczku używana będzie wtyczka usługi Azure Storage dla ciągłej integracji Jenkins udostępnianych przez firmę Microsoft.

## <a name="jenkins-overview"></a>Omówienie usługi Jenkins
Jenkins umożliwia ciągłą integrację z projektem oprogramowania, umożliwiając deweloperom łatwe integrowanie ich zmian w kodzie i mieć kompilacje generowane automatycznie i często, a tym samym zwiększenia produktywności deweloperów. Kompilacje są wersjonowane i artefaktów kompilacji można przekazać do różnych repozytoriów. W tym artykule pokazano, jak używać magazynu obiektów blob platformy Azure jako repozytorium artefaktów kompilacji. Widoczna będzie również sposób pobierania zależności z magazynu obiektów blob platformy Azure.

Więcej informacji na temat usługi Jenkins, można znaleźć w folderze [spełniają Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Korzyści z używania usługi obiektów Blob
Korzyści z używania usługi obiektów Blob, aby hostować swoje artefakty kompilacji zwinne Wytwarzanie oprogramowania obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub zależności do pobrania.
* Wydajność, gdy rozwiązanie ciągłej integracji Jenkins przekazuje swoje artefakty kompilacji.
* Wydajność, gdy klienci i partnerzy pobierają swoje artefakty kompilacji.
* Kontrolę nad zasad dostępu użytkownika, z możliwością wyboru między dostępu anonimowego dostępu sygnatury dostępu współdzielonego opartych na wygaśnięcie, prywatne, access, itp.

## <a name="prerequisites"></a>Wymagania wstępne
* Rozwiązanie ciągłej integracji narzędzia Jenkins.
  
    Jeśli obecnie nie masz rozwiązanie ciągłej integracji narzędzia Jenkins, możesz uruchomić rozwiązaniem ciągłej integracji narzędzia Jenkins przy użyciu następujących technik:
  
  1. Na maszynie z obsługą języka Java, Pobierz jenkins.war z <https://jenkins-ci.org>.
  2. W wierszu polecenia, który jest otwierany do folderu, który zawiera jenkins.war Uruchom polecenie:
     
      `java -jar jenkins.war`

  3. Otwórz w przeglądarce, `http://localhost:8080/` można otworzyć pulpitu nawigacyjnego usługi Jenkins, który będzie używany do instalowania i konfigurowania wtyczki usługi Azure Storage.
     
      Podczas gdy typowe rozwiązanie ciągłej integracji narzędzia Jenkins, należy skonfigurować do uruchamiania jako usługa, uruchomiona war narzędzia Jenkins w wierszu polecenia, będzie wystarczająca na potrzeby tego samouczka.
* Konto platformy Azure. Możesz zasubskrybować konta platformy Azure w <https://www.azure.com>.
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz utworzyć ją przy użyciu czynności opisane w temacie [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).
* Znajomość rozwiązanie ciągłej integracji Jenkins jest zalecane, ale nie jest to wymagane, zgodnie z następującą zawartością użyje podstawowy przykład w celu dowiesz się, że artefakty kompilacji kroki wymagane w przypadku korzystania z usługi Blob service jako repozytorium do ciągłej integracji narzędzia Jenkins.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jak używać usługi obiektów Blob przy użyciu ciągłej integracji Jenkins
Do korzystania z usługi obiektów Blob przy użyciu narzędzia Jenkins, musisz zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do używania konta magazynu, a następnie utwórz akcji po kompilacji, która przekazuje swoje artefakty kompilacji do swojego konta magazynu. Te kroki opisano w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. W pulpicie nawigacyjnym usługi Jenkins wybierz **Zarządzaj serwerem Jenkins**.
2. W **Zarządzaj serwerem Jenkins** wybierz opcję **Zarządzaj wtyczkami**.
3. Wybierz kartę **Available** (Dostępne).
4. W **osób przekazujących artefaktu** sekcji wyboru **dodatek usługi Microsoft Azure Storage**.
5. Wybierz opcję **zainstalować bez klauzuli restart** lub **Pobierz i zainstaluj po ponownym uruchomieniu**.
6. Uruchom ponownie usługi Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurowanie wtyczki usługi Azure Storage do używania konta magazynu
1. W pulpicie nawigacyjnym usługi Jenkins wybierz **Zarządzaj serwerem Jenkins**.
2. W **Zarządzaj serwerem Jenkins** wybierz opcję **Konfiguruj System**.
3. W **konfigurację konta magazynu Azure Microsoft** sekcji:
   1. Wprowadź nazwę konta magazynu, którego można uzyskać z [witryny Azure Portal](https://portal.azure.com).
   2. Wprowadź klucz konta magazynu, również u: [witryny Azure Portal](https://portal.azure.com).
   3. Użyj wartości domyślnej dla **adres URL punktu końcowego usługi Blob** korzystania z chmury globalnej platformy Azure. Jeśli używasz innej chmurze platformy Azure, używać punktu końcowego, jak to określono w [witryny Azure Portal](https://portal.azure.com) konta magazynu. 
   4. Wybierz **przeprowadzenie walidacji poświadczeń magazynu** do zweryfikowania konta magazynu. 
   5. [Opcjonalnie] Jeśli masz dodatkowe konta magazynu, które mają udostępnione do ciągłej integracji narzędzia Jenkins, wybierz **dodać więcej kont magazynu**.
   6. Wybierz **Zapisz** można zapisać ustawień.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcji wykonywanych po kompilacji, która przekazuje swoje artefakty kompilacji do swojego konta magazynu
Do celów instruktażowych należy najpierw utworzyć zadanie, które będzie utworzyć kilka plików, a następnie dodaj w działaniu po kompilacji, aby przekazać pliki do swojego konta magazynu.

1. W pulpicie nawigacyjnym usługi Jenkins wybierz **nowy element**.
2. Nadaj nazwę zadaniu **MyJob**, wybierz opcję **kompilowania projektu oprogramowania stylu**, a następnie wybierz pozycję **OK**.
3. W **kompilacji** sekcji konfiguracji zadania, wybierz opcję **Dodaj krok kompilacji** i wybierz **Windows wykonania polecenia batch**.
4. W **polecenia**, użyj następujących poleceń:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. W **akcjach wykonywanych po kompilacji** sekcji konfiguracji zadania, wybierz opcję **Dodawanie akcji wykonywanych po kompilacji** i wybierz **Przekaż artefakty do usługi Azure Blob storage**.
6. Aby uzyskać **nazwa konta magazynu**, wybierz konto magazynu do użycia.
7. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera. (Zostanie utworzony kontener Jeśli go jeszcze nie istnieje przekazywane artefaktów kompilacji.) Możesz użyć zmiennych środowiskowych, dlatego w tym przykładzie należy wprowadzić `${JOB_NAME}` jako nazwę kontenera.
   
    **Porada**
   
    Poniżej **polecenia** sekcji, w którym wprowadzono skryptu dla **Windows wykonania polecenia batch** łącze do zmiennych środowiskowych rozpoznawane przez narzędzia Jenkins. Wybierz ten link, aby dowiedzieć się, nazwy zmiennych środowiskowych i opisy. Zmienne środowiskowe, które zawierają znaki specjalne, takie jak **BUILD_URL** zmiennej środowiskowej, nie są dozwolone jako nazwa kontenera lub wspólnej ścieżki wirtualnej.
8. Wybierz **upublicznić domyślnie nowy kontener** w tym przykładzie. (Jeśli chcesz użyć kontenera prywatnych, należy utworzyć sygnaturę dostępu współdzielonego, aby zezwolić na dostęp, co wykracza poza zakres tego artykułu. Dowiedz się więcej na temat sygnatur dostępu współdzielonego w [za pomocą sygnatur dostępu udostępnionego (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcjonalnie] Wybierz **czyste kontenera przed przekazaniem** Jeśli chcesz, aby kontener do wyczyszczenia zawartości przed artefakty kompilacji są przekazywane (pozostaw to pole niezaznaczone, jeśli nie chcesz wyczyścić zawartość kontenerów).
10. Aby uzyskać **listy artefakty do przekazania**, wprowadź `text/*.txt`.
11. Dla **wspólnej ścieżki wirtualnej dla przekazanych artefaktów**dla celów tego samouczka wprowadź `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Wybierz **Zapisz** można zapisać ustawień.
13. Na pulpicie nawigacyjnym usługi Jenkins wybierz **Kompiluj teraz** do uruchomienia **MyJob**. Sprawdź dane wyjściowe konsoli z stanu. Komunikaty o stanie dla usługi Azure storage będą uwzględniane w danych wyjściowych konsoli, po uruchomieniu akcji wykonywanych po kompilacji, można przekazać artefaktów kompilacji.
14. Po pomyślnym zakończeniu zadania można sprawdzić artefaktów kompilacji, otwierając publicznego obiektu blob.
    1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
    2. Wybierz pozycję **Magazyn**.
    3. Wybierz nazwę konta magazynu używany dla serwera Jenkins.
    4. Wybierz **kontenery**.
    5. Wybierz kontener o nazwie **myjob**, czyli małe wersję nazwę zadania, przypisana podczas tworzenia zadania narzędzia Jenkins. Nazwy kontenerów i obiektów blob są małe litery (i wielkość liter) w usłudze Azure storage. W obrębie listy obiektów blob w kontenerze o nazwie **myjob**, powinien zostać wyświetlony **hello.txt** i **date.txt**. Skopiuj adres URL dla dowolnego z tych elementów, a następnie otwórz go w przeglądarce. Plik tekstowy, który został przekazany zostanie wyświetlona jako artefakt kompilacji.

Poszczególne zadania można utworzyć tylko jedną akcje po kompilacji, która przekazuje artefakty do usługi Azure blob storage. Jednej akcji wykonywanych po kompilacji do przekazania artefaktów w usłudze Azure blob storage można określić różne pliki (łącznie z symbolami wieloznacznymi) i ścieżki do plików w ramach **listy artefakty do przekazania** przy użyciu średnika jako separatora. Na przykład, jeśli kompilacji usługi Jenkins tworzy pliki JAR i pliki TXT w Twoim obszarze roboczym **kompilacji** folderu i chcesz przekazać zarówno do magazynu obiektów blob platformy Azure, użyj następującą wartość dla **listy artefakty do przekazania** opcja: `build/\*.jar;build/\*.txt`. Składnia dwoma dwukropkami umożliwia również określić ścieżkę do użycia w nazwie obiektu blob. Na przykład, jeśli chcesz, aby plikach JAR, aby zostać przekazane za pomocą **pliki binarne** ścieżka obiektu blob i plików TXT, aby zostać przekazane za pomocą **uwagi** w ścieżce obiektu blob, użyj następującej wartości dla **listę Artefakty do przekazania** opcja: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji, który pobiera z usługi Azure blob storage
Poniższe kroki ilustrują skonfigurować krok kompilacji, można pobrać elementów z magazynu obiektów blob platformy Azure, co jest przydatne, jeśli chcesz dołączyć elementy do kompilacji. Przykładem użycia tego wzorca jest plikach JAR, które możesz chcieć zachować w usłudze Azure blob storage.

1. W **kompilacji** sekcji konfiguracji zadania, wybierz opcję **Dodaj krok kompilacji** i wybierz **pobierać z usługi Azure Blob storage**.
2. Aby uzyskać **nazwa konta magazynu**, wybierz konto magazynu do użycia.
3. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera, który zawiera obiekty BLOB, którą chcesz pobrać. Można używać zmiennych środowiskowych.
4. Aby uzyskać **nazwa obiektu Blob**, określ nazwę obiektu blob. Można używać zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu znakami nazwy obiektu blob. Na przykład **projektu\\** * należałoby określić wszystkie obiekty BLOB, których nazwy rozpoczynają się od **projektu**.
5. [Opcjonalnie] Aby uzyskać **ścieżkę pobierania**, określ ścieżkę na maszynie Jenkins, której chcesz pobierać pliki z usługi Azure blob storage. Można także zmienne środowiskowe. (Jeśli nie zostanie określona wartość **ścieżkę pobierania**, pliki z magazynu obiektów blob platformy Azure zostaną pobrane z obszarem roboczym zadania.)

Jeśli masz dodatkowe elementy, które chcesz pobrać z magazynu obiektów blob platformy Azure, możesz utworzyć dodatkowych kroków kompilacji.

Po uruchomieniu kompilacji, można sprawdzić dane wyjściowe konsoli z kompilacji historii lub Przyjrzyj się do lokalizacji pobierania, aby zobaczyć, czy obiekty BLOB, które miały zostały pomyślnie pobrane.  

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez usługę obiektów Blob
Ta sekcja zawiera omówienie składników usługi obiektów Blob.

* **Konto magazynu**: Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Konto magazynu to najwyższy poziom przestrzeni nazw do uzyskiwania dostępu do obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, tak długo, jak ich łączny rozmiar wynosi poniżej 100 TB.
* **kontener**: Kontener zawiera grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt blob**: Plik dowolnego typu i rozmiaru. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blokowe i stronicowe obiekty BLOB. Większość plików to blokowe obiekty BLOB. Pojedynczy blokowy obiekt blob może być maksymalnie 200 GB. W tym samouczku korzysta z blokowych obiektów blob. Stronicowe obiekty BLOB, innego typu obiektu blob, może być maksymalnie 1 TB, rozmiar i są bardziej efektywne, gdy zakresów bajtów w pliku są często modyfikowane. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [omówienie blokowych obiektów blob, Uzupełnialnych obiektów blob i stronicowe obiekty BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL**: Obiekty BLOB są adresy, przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Format powyżej ma zastosowanie do globalnego chmury platformy Azure. Jeśli używasz innej chmurze platformy Azure, użyj punktu końcowego w ramach [witryny Azure Portal](https://portal.azure.com) Aby określić adres URL punktu końcowego.)
  
    W powyższym formacie `storageaccount` reprezentuje nazwę konta magazynu `container_name` reprezentuje nazwę kontenera, i `blob_name` reprezentuje nazwę obiektu blob, odpowiednio. W ramach nazwa kontenera może mieć wiele ścieżek oddzielonych ukośnikiem, **/** . Przykładowa nazwa kontenera używany na potrzeby tego samouczka został **MyJob**, i **${kompilacji\_identyfikator} / ${kompilacji\_numer}** został użyty podczas wspólnej ścieżki wirtualnej skutkuje o adres URL obiektu blob o następującej postaci:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki
* [Spełnia usługi Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Usługa Azure Storage SDK dla języka Java](https://github.com/azure/azure-storage-java)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).
