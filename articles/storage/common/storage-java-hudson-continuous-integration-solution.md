---
title: Jak używać Hudson z pamięcią Blob | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać hudson z magazynu obiektów Blob platformy Azure jako repozytorium artefaktów kompilacji.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201389"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem hudson do ciągłej integracji
## <a name="overview"></a>Omówienie
Poniżej przedstawiono sposób używania magazynu obiektów Blob jako repozytorium artefaktów kompilacji utworzonych przez rozwiązanie Hudson Continuous Integration (CI) lub jako źródło plików do pobrania, które mają być używane w procesie kompilacji. Jednym ze scenariuszy, w których można znaleźć to przydatne jest, gdy kodujesz w środowisku programistycznym agile (przy użyciu języka Java lub innych języków), kompilacje są uruchomione w oparciu o ciągłą integrację i potrzebujesz repozytorium artefaktów kompilacji, dzięki czemu można, udostępnij je na przykład innym członkom organizacji, klientom lub zachowaj archiwum.  Innym scenariuszem jest, gdy zadanie kompilacji sam wymaga innych plików, na przykład zależności do pobrania w ramach danych wejściowych kompilacji.

W tym samouczku będziesz używać wtyczki usługi Azure Storage dla hudson CI udostępnionej przez firmę Microsoft.

## <a name="introduction-to-hudson"></a>Wprowadzenie do Hudsona
Hudson umożliwia ciągłą integrację projektu oprogramowania, umożliwiając deweloperom łatwe zintegrowanie zmian w kodzie i tworzenie kompilacji produkowanych automatycznie i często, zwiększając w ten sposób produktywność programistów. Kompilacje są wersjonowane, a artefakty kompilacji mogą być przekazywane do różnych repozytoriów. W tym artykule pokazano, jak używać magazynu obiektów Blob platformy Azure jako repozytorium artefaktów kompilacji. Pokaże również, jak pobrać zależności z magazynu obiektów Blob platformy Azure.

Więcej informacji na temat Hudsona można znaleźć na [stronie Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Korzyści z korzystania z usługi obiektów Blob
Korzyści z używania usługi obiektów Blob do hostowania artefaktów kompilacji agile development obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub zależności do pobrania.
* Wydajność, gdy rozwiązanie Hudson CI przesyła artefakty kompilacji.
* Wydajność, gdy twoi klienci i partnerzy pobierają artefakty kompilacji.
* Kontrola nad zasadami dostępu użytkowników, z wyborem między dostępem anonimowym, dostępem do podpisu dostępu współdzielonego opartego na wygaśnięciu, dostępem prywatnym itp.

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z usługi blob z rozwiązaniem Hudson CI, potrzebne będą następujące elementy:

* Rozwiązanie Hudson Ciągłej Integracji.
  
    Jeśli obecnie nie masz rozwiązania Hudson CI, możesz uruchomić rozwiązanie Hudson CI przy użyciu następującej techniki:
  
  1. Na komputerze obsługującym technologię Java [pobierz plik Hudson WAR](https://www.eclipse.org/hudson/download.php).
  2. W wierszu polecenia, który jest otwarty do folderu, który zawiera Hudson WAR, uruchom Hudson WAR. Na przykład, jeśli pobrano wersję 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. W przeglądarce `http://localhost:8080/`otwórz plik . Spowoduje to otwarcie pulpitu nawigacyjnego Hudsona.
  4. Przy pierwszym użyciu Hudsona, należy `http://localhost:8080/`ukończyć wstępną konfigurację w .
  5. Po zakończeniu początkowej konfiguracji anuluj uruchomione wystąpienie wojny Hudsona, uruchom ponownie `http://localhost:8080/`hudson war i ponownie otwórz pulpit nawigacyjny Hudsona, który będzie używany do instalowania i konfigurowania wtyczki usługi Azure Storage.
     
      Podczas gdy typowe rozwiązanie Hudson CI zostanie skonfigurowane do uruchamiania jako usługa, uruchomienie wojny Hudsona w wierszu polecenia będzie wystarczające dla tego samouczka.
* Konto platformy Azure. Możesz założyć konto platformy Azure <https://www.azure.com>w pliku .
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz je utworzyć, wykonując czynności opisane w [umiań tworzenia konta magazynu](../common/storage-account-create.md).
* Znajomość rozwiązania Hudson CI jest zalecane, ale nie jest wymagane, ponieważ następująca zawartość użyje podstawowego przykładu, aby pokazać kroki potrzebne podczas korzystania z usługi obiektów Blob jako repozytorium artefaktów kompilacji kompilacji Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Jak korzystać z usługi Blob z Hudson CI
Aby korzystać z usługi blob z Hudson, należy zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do korzystania z konta magazynu, a następnie utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu. Te kroki są opisane w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. W panelu hudson kliknij pozycję **Zarządzaj Hudsonem**.
2. Na stronie **Zarządzanie Hudsonem** kliknij pozycję **Zarządzaj wtyczkami**.
3. Kliknij kartę **Dostępne.**
4. Kliknij **pozycję Inne**.
5. W sekcji **Przekazywanie artefaktów** wybierz **wtyczkę Usługi Microsoft Azure Storage**.
6. Kliknij **pozycję Zainstaluj**.
7. Po zakończeniu instalacji uruchom ponownie hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak skonfigurować wtyczkę usługi Azure Storage do używania konta magazynu
1. W panelu hudson kliknij pozycję **Zarządzaj Hudsonem**.
2. Na stronie **Zarządzanie Hudsonem** kliknij pozycję **Konfiguruj system**.
3. W sekcji **Konfiguracja konta usługi Microsoft Azure Storage:**
   
    a. Wprowadź nazwę konta magazynu, którą można uzyskać w [witrynie Azure portal](https://portal.azure.com).
   
    b. Wprowadź klucz konta magazynu, który można uzyskać również w [witrynie Azure portal](https://portal.azure.com).
   
    d. Użyj wartości domyślnej dla **adresu URL punktu końcowego usługi obiektów blob,** jeśli używasz globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego, jak określono w [witrynie Azure portal](https://portal.azure.com) dla konta magazynu.
   
    d. Kliknij **pozycję Sprawdź poprawność poświadczeń magazynu,** aby sprawdzić poprawność konta magazynu.
   
    e. [Opcjonalnie] Jeśli masz dodatkowe konta magazynu, które chcesz udostępnić firmie Hudson CI, kliknij pozycję **Dodaj więcej kont magazynu**.
   
    f. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu
Na potrzeby instrukcji najpierw musimy utworzyć zadanie, które utworzy kilka plików, a następnie dodać akcję po kompilacji, aby przekazać pliki na konto magazynu.

1. Na pulpicie nawigacyjnym Hudsona kliknij pozycję **Nowa praca**.
2. Nazwij zadanie **MyJob**, kliknij przycisk **Zbuduj zadanie wolnego stylu oprogramowania**, a następnie kliknij przycisk **OK**.
3. W sekcji **Kompilacja** konfiguracji zadania kliknij pozycję **Dodaj krok kompilacji** i wybierz polecenie **Wykonaj wsad systemu Windows**.
4. W **poleceniu**użyj następujących poleceń:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. W sekcji **Akcje po kompilacji** konfiguracji zadania kliknij pozycję **Przekaż artefakty do magazynu obiektów Blob platformy Microsoft Azure**.
6. W przypadku **nazwy konta magazynu**wybierz konto magazynu, którego chcesz użyć.
7. W obszarze **Nazwa kontenera**określ nazwę kontenera. (Kontener zostanie utworzony, jeśli jeszcze nie istnieje, gdy artefakty kompilacji są przekazywane.) Można użyć zmiennych środowiskowych, więc w tym przykładzie wprowadź **${JOB_NAME}** jako nazwę kontenera.
   
    **Wskazówka**
   
    Poniżej sekcji **Command,** w której wprowadzono skrypt **polecenia wsadowego Wykonywanie systemu Windows,** znajduje się łącze do zmiennych środowiskowych rozpoznawanych przez Hudsona. Kliknij to łącze, aby poznać nazwy i opisy zmiennych środowiskowych. Zmienne środowiskowe zawierające znaki specjalne, takie jak **zmienna środowiskowa BUILD_URL,** nie są dozwolone jako nazwa kontenera lub wspólna ścieżka wirtualna.
8. W tym przykładzie kliknij **pozycję Domyślnie udostępnij nowy kontener.** (Jeśli chcesz użyć kontenera prywatnego, musisz utworzyć podpis dostępu współdzielonego, aby zezwolić na dostęp. To wykracza poza zakres tego artykułu. Więcej informacji na temat podpisów dostępu współdzielonego można uzyskać na stronie [Using Shared Access Signatures (SAS).](storage-sas-overview.md)
9. [Opcjonalnie] Kliknij **przycisk Wyczyść kontener przed przekazaniem,** jeśli chcesz, aby kontener został wyczyszczony z zawartości przed przekazaniem artefaktów kompilacji (pozostaw go niezaznaczone, jeśli nie chcesz czyścić zawartości kontenera).
10. Aby **wyświetlić listę artefaktów do przesłania,** wpisz **tekst/*.txt**.
11. W przypadku **wspólnej ścieżki wirtualnej dla przekazanych artefaktów**wpisz **\_${BUILD ID}/${BUILD\_NUMBER}**.
12. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.
13. Na pulpicie nawigacyjnym Hudson kliknij przycisk **Buduj teraz,** aby uruchomić **program MyJob**. Sprawdź dane wyjściowe konsoli pod kątem stanu. Komunikaty o stanie usługi Azure Storage zostaną uwzględnione w danych wyjściowych konsoli, gdy akcja po kompilacji rozpocznie przekazywanie artefaktów kompilacji.
14. Po pomyślnym zakończeniu zadania, można zbadać artefakty kompilacji, otwierając publiczny obiekt blob.
    
    a. Zaloguj się do [Portalu Azure](https://portal.azure.com).
    
    b. Kliknij **pozycję Magazyn**.
    
    d. Kliknij nazwę konta magazynu, która została użyta w hudsonie.
    
    d. Kliknij pozycję **Kontenery**.
    
    e. Kliknij kontener o nazwie **myjob**, który jest wersją małych liter nazwy zadania przypisanej podczas tworzenia zadania Hudsona. Nazwy kontenerów i nazwy obiektów blob są małe (i rozróżniana wielkość liter) w usłudze Azure Storage. Na liście obiektów blob dla kontenera o nazwie **myjob** powinieneś zobaczyć **hello.txt** i **date.txt**. Skopiuj adres URL jednego z tych elementów i otwórz go w przeglądarce. Zostanie wyświetlony plik tekstowy, który został przekazany jako artefakt kompilacji.

Tylko jedna akcja po kompilacji, która przekazuje artefakty do magazynu obiektów Blob platformy Azure można utworzyć dla jednego zadania. Pojedyncza akcja po kompilacji do przekazywania artefaktów do magazynu obiektów Blob platformy Azure można określić różne pliki (w tym symbole wieloznaczne) i ścieżki do plików w **ramach listy artefaktów do przekazania** przy użyciu średnika jako separatora. Jeśli na przykład kompilacja Hudsona tworzy pliki JAR i pliki TXT w folderze **kompilacji** obszaru roboczego i chcesz przekazać oba do magazynu obiektów Blob platformy Azure, użyj następującej opcji dla **listy artefaktów, aby przekazać** wartość: **build/\*.jar;build/\*.txt**. Można również użyć składni dwukroęcza, aby określić ścieżkę do użycia w nazwie obiektu blob. Na przykład, jeśli chcesz, aby usługi JA Zostały przesłane przy użyciu **plików binarnych** w ścieżce obiektów blob, a pliki TXT zostały przesłane za pomocą **powiadomień** w ścieżce obiektu blob, użyj następującej opcji dla **listy artefaktów, aby przekazać** wartość: **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji, który jest pobierany z magazynu obiektów Blob platformy Azure
Poniższe kroki pokazują, jak skonfigurować krok kompilacji, aby pobrać elementy z magazynu obiektów blob platformy Azure. Byłoby to przydatne, jeśli chcesz dołączyć elementy w kompilacji, na przykład JARs, które można zachować w magazynie obiektów Blob platformy Azure.

1. W sekcji **Kompilacja** konfiguracji zadania kliknij pozycję **Dodaj krok kompilacji** i wybierz pozycję **Pobierz z magazynu obiektów Blob platformy Azure**.
2. W obszarze **Nazwa konta magazynowania**wybierz konto magazynu, którego chcesz użyć.
3. W **obszarze Nazwa kontenera**określ nazwę kontenera, który ma obiekty blob, które chcesz pobrać. Można użyć zmiennych środowiskowych.
4. W przypadku **nazwy obiektu Blob**określ nazwę obiektu blob. Można użyć zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu początkowych liter nazwy obiektu blob. Na przykład **\\projekt*** określi wszystkie obiekty blob, których nazwy zaczynają się od **projektu**.
5. [Opcjonalnie] Aby **pobrać ścieżkę,** określ ścieżkę na komputerze Hudson, gdzie chcesz pobrać pliki z magazynu obiektów Blob platformy Azure. Można również użyć zmiennych środowiskowych. (Jeśli nie podasz wartości **ścieżki pobierania,** pliki z magazynu obiektów Blob platformy Azure zostaną pobrane do obszaru roboczego zadania).

Jeśli masz dodatkowe elementy, które chcesz pobrać z magazynu obiektów Blob platformy Azure, możesz utworzyć dodatkowe kroki kompilacji.

Po uruchomieniu kompilacji można sprawdzić dane wyjściowe konsoli historii kompilacji lub przyjrzeć się lokalizacji pobierania, aby sprawdzić, czy oczekiwane obiekty blob zostały pomyślnie pobrane.

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez usługę obiektów Blob
Poniżej przedstawiono omówienie składników usługi obiektów Blob.

* **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Jest to najwyższy poziom obszaru nazw dostępu do obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, o ile ich całkowity rozmiar jest poniżej 100 TB.
* **Kontener:** Kontener zapewnia grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt blob**: Plik dowolnego typu i rozmiaru. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blok i strony obiektów blob. Większość plików to blokowe obiekty blob. Pojedynczy blokowy obiekt blob może mieć rozmiar do 200 GB. W tym samouczku użyto bloków obiektów blob. Obiekty BLOB strony, inny typ obiektu blob, może mieć rozmiar do 1 TB i są bardziej wydajne, gdy zakresy bajtów w pliku są często modyfikowane. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [Opis bloków obiektów blob, dołączanie obiektów blob i obiektów blob strony](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL:** obiekty blob można adresować przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Powyższy format ma zastosowanie do globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego w [witrynie Azure portal,](https://portal.azure.com) aby określić punkt końcowy adresu URL.)
  
    W powyższym `storageaccount` formacie reprezentuje nazwę konta `container_name` magazynu, reprezentuje nazwę kontenera i `blob_name` reprezentuje nazwę obiektu blob, odpowiednio. W nazwie kontenera można mieć wiele ścieżek oddzielonych **/** ukośnikiem do przodu, . Przykładowa nazwa kontenera w tym samouczku to **MyJob**, a **\_${BUILD ID}/${BUILD\_NUMBER}** został użyty dla wspólnej ścieżki wirtualnej, co spowodowało, że obiekt blob ma adres URL następującego formularza:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Następne kroki
* [Poznaj Hudsona](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).
