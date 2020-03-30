---
title: Korzystanie z usługi Azure Storage z rozwiązaniem do ciągłej integracji usługi Jenkins
description: W tym samouczku pokazano, jak używać usługi obiektów blob platformy Azure jako repozytorium artefaktów kompilacji utworzonych przez rozwiązanie integracji ciągłej usługi Jenkins.
keywords: jenkins, azure, devops, magazynowanie, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624685"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem do ciągłej integracji usługi Jenkins

W tym artykule pokazano, jak używać magazynu obiektów Blob jako repozytorium artefaktów kompilacji utworzonych przez rozwiązanie ciągłej integracji usługi Jenkins (CI) lub jako źródło plików do pobrania, które mają być używane w procesie kompilacji. Jednym ze scenariuszy, w których można znaleźć to rozwiązanie przydatne jest, gdy kodujesz w środowisku programistycznym agile (przy użyciu języka Java lub innych języków), kompilacje są uruchomione w oparciu o ciągłą integrację i potrzebujesz repozytorium artefaktów kompilacji, dzięki czemu można na przykład udostępnić je innym członkom organizacji, klientom lub prowadzić archiwum. Innym scenariuszem jest, gdy zadanie kompilacji sam wymaga innych plików, na przykład zależności do pobrania w ramach danych wejściowych kompilacji.

W tym samouczku będzie można użyć wtyczki usługi Azure Storage dla usługi Jenkins CI udostępnione przez firmę Microsoft.

## <a name="jenkins-overview"></a>Omówienie usługi Jenkins

Jenkins umożliwia ciągłą integrację projektu oprogramowania, umożliwiając deweloperom łatwe zintegrowanie zmian kodu i tworzenie kompilacji produkowanych automatycznie i często, zwiększając w ten sposób produktywność deweloperów. Kompilacje są wersjonowane, a artefakty kompilacji mogą być przekazywane do różnych repozytoriów. W tym artykule pokazano, jak używać magazynu obiektów blob platformy Azure jako repozytorium artefaktów kompilacji. Pokaże również, jak pobrać zależności z magazynu obiektów blob platformy Azure.

Więcej informacji na temat usługi Jenkins można znaleźć na stronie [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Korzyści z korzystania z usługi obiektów Blob

Korzyści z używania usługi obiektów Blob do hostowania artefaktów kompilacji agile development obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub zależności do pobrania.
* Wydajność, gdy rozwiązanie ci usługi Jenkins przekazuje artefakty kompilacji.
* Wydajność, gdy twoi klienci i partnerzy pobierają artefakty kompilacji.
* Kontrola nad zasadami dostępu użytkowników, z wyborem między dostępem anonimowym, dostępem do podpisu dostępu współdzielonego opartego na wygaśnięciu, dostępem prywatnym itp.

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie ciągłej integracji jenkins.
  
    Jeśli obecnie nie masz rozwiązania usługi Jenkins CI, można uruchomić rozwiązanie ci usługi Jenkins przy użyciu następującej techniki:
  
  1. Na komputerze obsługującym technologię Java <https://jenkins-ci.org>pobierz plik jenkins.war z pliku .
  2. W wierszu polecenia otwieranym do folderu zawierającego plik jenkins.war uruchom:
     
      `java -jar jenkins.war`

  3. W przeglądarce `http://localhost:8080/` otwórz pulpit nawigacyjny usługi Jenkins, który będzie używany do instalowania i konfigurowania wtyczki usługi Azure Storage.
     
      Podczas gdy typowe rozwiązanie ci usługi Jenkins zostanie skonfigurowane do uruchamiania jako usługa, uruchomienie wojny jenkins w wierszu polecenia będzie wystarczające dla tego samouczka.
* Konto platformy Azure. Możesz założyć konto platformy Azure <https://www.azure.com>w pliku .
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz je utworzyć, wykonując czynności opisane w [umiań tworzenia konta magazynu](../storage/common/storage-account-create.md).
* Znajomość rozwiązania ci usługi Jenkins jest zalecane, ale nie jest wymagane, ponieważ następująca zawartość użyje podstawowego przykładu, aby pokazać kroki potrzebne podczas korzystania z usługi obiektów Blob jako repozytorium artefaktów kompilacji ci usługi Jenkins.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jak korzystać z usługi obiektów Blob z usługą Jenkins CI
Aby korzystać z usługi obiektów Blob z usługą Jenkins, należy zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do korzystania z konta magazynu, a następnie utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu. Te kroki są opisane w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. Na pulpicie nawigacyjnym aplikacji Jenkins wybierz pozycję **Zarządzaj u jenkinsem**.
2. Na stronie **Zarządzanie jenkinsem** wybierz pozycję **Zarządzaj wtyczkami**.
3. Wybierz kartę **Available** (Dostępne).
4. W sekcji **Przekazywanie artefaktów** sprawdź **wtyczkę Usługi Microsoft Azure Storage**.
5. Wybierz opcję **Zainstaluj bez restartu** lub **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.
6. Uruchom ponownie usługę Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak skonfigurować wtyczkę usługi Azure Storage do używania konta magazynu
1. Na pulpicie nawigacyjnym aplikacji Jenkins wybierz pozycję **Zarządzaj u jenkinsem**.
2. Na stronie **Zarządzanie jenkinsem** wybierz pozycję **Konfiguruj system**.
3. W sekcji **Konfiguracja konta usługi Microsoft Azure Storage:**
   1. Wprowadź nazwę konta magazynu, którą można uzyskać w [witrynie Azure portal](https://portal.azure.com).
   2. Wprowadź klucz konta magazynu, który można uzyskać również w [witrynie Azure portal](https://portal.azure.com).
   3. Użyj wartości domyślnej dla **adresu URL punktu końcowego usługi obiektów blob,** jeśli używasz globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego, jak określono w [witrynie Azure portal](https://portal.azure.com) dla konta magazynu. 
   4. Wybierz **pozycję Sprawdź poprawność poświadczeń magazynu,** aby sprawdzić poprawność konta magazynu. 
   5. [Opcjonalnie] Jeśli masz dodatkowe konta magazynu, które mają być dostępne dla ci usługi Jenkins, wybierz pozycję **Dodaj więcej kont magazynu**.
   6. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu
Do celów instruktażowych należy najpierw utworzyć zadanie, które utworzy kilka plików, a następnie dodać w akcji po kompilacji, aby przekazać pliki do konta magazynu.

1. Na pulpicie nawigacyjnym aplikacji Jenkins wybierz pozycję **Nowy element**.
2. Nazwij zadanie **MyJob**, wybierz **pozycję Zbuduj projekt wolnego oprogramowania,** a następnie wybierz przycisk **OK**.
3. W sekcji **Kompilacja** konfiguracji zadania wybierz pozycję **Dodaj krok kompilacji** i wybierz polecenie **Wykonaj wsad systemu Windows**.
4. W **poleceniu**użyj następujących poleceń:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. W sekcji **Akcje po kompilacji** konfiguracji zadania wybierz pozycję **Dodaj akcję po kompilacji** i wybierz pozycję **Przekaż artefakty do magazynu obiektów Blob platformy Azure**.
6. W obszarze **Nazwa konta magazynowania**wybierz konto magazynu, którego chcesz użyć.
7. W **obszarze Nazwa kontenera**określ nazwę kontenera. (Kontener zostanie utworzony, jeśli jeszcze nie istnieje, gdy artefakty kompilacji są przekazywane.) Można użyć zmiennych środowiskowych, więc `${JOB_NAME}` w tym przykładzie wprowadź jako nazwę kontenera.
   
    **Wskazówka**
   
    Poniżej sekcji **Command,** w której wprowadzono skrypt **polecenia wsadowego Wykonywanie systemu Windows,** znajduje się łącze do zmiennych środowiskowych rozpoznawanych przez firmę Jenkins. Wybierz to łącze, aby poznać nazwy i opisy zmiennych środowiskowych. Zmienne środowiskowe zawierające znaki specjalne, takie jak **zmienna środowiskowa BUILD_URL,** nie są dozwolone jako nazwa kontenera lub wspólna ścieżka wirtualna.
8. W tym przykładzie wybierz **pozycję Domyślnie udostępnij nowy kontener.** (Jeśli chcesz użyć kontenera prywatnego, musisz utworzyć podpis dostępu współdzielonego, aby zezwolić na dostęp, który wykracza poza zakres tego artykułu. Więcej informacji na temat podpisów dostępu współdzielonego można uzyskać na stronie [Using Shared Access Signatures (SAS).](../storage/common/storage-sas-overview.md)
9. [Opcjonalnie] Wybierz **Clean kontenera przed przekazaniem,** jeśli chcesz kontenera, które mają być wyczyszczone zawartości przed kompilacji artefakty są przekazywane (pozostawić niezaznaczone, jeśli nie chcesz czyścić zawartość kontenera).
10. Aby **wyświetlić listę artefaktów do przesłania,** wprowadź `text/*.txt`.
11. Dla **wspólnej ścieżki wirtualnej dla przesłanych artefaktów**, `${BUILD\_ID}/${BUILD\_NUMBER}`na potrzeby tego samouczka, wprowadź .
12. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.
13. Na pulpicie nawigacyjnym usługi Jenkins wybierz pozycję **Zbuduj teraz,** aby uruchomić **program MyJob**. Sprawdź dane wyjściowe konsoli pod kątem stanu. Komunikaty o stanie usługi Azure storage zostaną uwzględnione w danych wyjściowych konsoli, gdy akcja po kompilacji rozpocznie przekazywanie artefaktów kompilacji.
14. Po pomyślnym zakończeniu zadania, można zbadać artefakty kompilacji, otwierając publiczny obiekt blob.
    1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
    2. Wybierz **opcję Magazyn**.
    3. Wybierz nazwę konta magazynu, która jest używana dla usługi Jenkins.
    4. Wybierz **kontenery**.
    5. Wybierz kontener o nazwie **myjob**, który jest niższą wersją nazwy zadania przypisanej podczas tworzenia zadania Jenkins. Nazwy kontenerów i nazwy obiektów blob są małe (i rozróżniana wielkość liter) w magazynie platformy Azure. Na liście obiektów blob dla kontenera o nazwie **myjob**powinien zostać wyświetlony **hello.txt** i **date.txt**. Skopiuj adres URL jednego z tych elementów i otwórz go w przeglądarce. Zostanie wyświetlony plik tekstowy, który został przekazany jako artefakt kompilacji.

Tylko jedna akcja po kompilacji, która przekazuje artefakty do magazynu obiektów blob platformy Azure można utworzyć na zadanie. Pojedyncza akcja po kompilacji do przekazywania artefaktów do magazynu obiektów blob platformy Azure można określić różne pliki (w tym symbole wieloznaczne) i ścieżki do plików w **ramach listy artefaktów do przekazania** przy użyciu średnika jako separatora. Jeśli na przykład kompilacja usługi Jenkins tworzy pliki JAR i pliki TXT w folderze **kompilacji** obszaru roboczego i chcesz przekazać oba do magazynu `build/\*.jar;build/\*.txt`obiektów blob platformy Azure, użyj następującej wartości dla opcji Lista **artefaktów do przekazania:** . Można również użyć składni dwukroęcza, aby określić ścieżkę do użycia w nazwie obiektu blob. Na przykład, jeśli chcesz, aby usługi JA Zostały przesłane za pomocą **plików binarnych** w ścieżce obiektów blob, a pliki TXT, aby uzyskać `build/\*.jar::binaries;build/\*.txt::notices`przesłane za pomocą **powiadomień** w ścieżce obiektu blob, użyj następującej wartości dla opcji Lista **artefaktów do przekazania:** .

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji pobrany z magazynu obiektów blob platformy Azure
Poniższe kroki ilustrują, aby skonfigurować krok kompilacji do pobierania elementów z magazynu obiektów blob platformy Azure, co jest przydatne, jeśli chcesz dołączyć elementy w kompilacji. Przykładem użycia tego wzorca jest JARs, które można chcieć utrwalić w magazynie obiektów blob platformy Azure.

1. W sekcji **Kompilacja** konfiguracji zadania wybierz pozycję **Dodaj krok kompilacji** i wybierz **pozycję Pobierz z magazynu obiektów Blob platformy Azure**.
2. W obszarze **Nazwa konta magazynowania**wybierz konto magazynu, którego chcesz użyć.
3. W **obszarze Nazwa kontenera**określ nazwę kontenera, który ma obiekty blob, które chcesz pobrać. Można użyć zmiennych środowiskowych.
4. W przypadku **nazwy obiektu Blob**określ nazwę obiektu blob. Można użyć zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu początkowych liter nazwy obiektu blob. Na przykład **\\projekt*** określi wszystkie obiekty blob, których nazwy zaczynają się od **projektu**.
5. [Opcjonalnie] Aby **pobrać ścieżkę,** określ ścieżkę na komputerze jenkins, gdzie chcesz pobrać pliki z magazynu obiektów blob platformy Azure. Można również użyć zmiennych środowiskowych. (Jeśli nie podasz wartości **ścieżki pobierania,** pliki z magazynu obiektów blob platformy Azure zostaną pobrane do obszaru roboczego zadania).

Jeśli masz dodatkowe elementy, które chcesz pobrać z magazynu obiektów blob platformy Azure, możesz utworzyć dodatkowe kroki kompilacji.

Po uruchomieniu kompilacji można sprawdzić dane wyjściowe konsoli historii kompilacji lub przyjrzeć się lokalizacji pobierania, aby sprawdzić, czy oczekiwane obiekty blob zostały pomyślnie pobrane.  

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez usługę obiektów Blob
Ta sekcja zawiera omówienie składników usługi obiektów Blob.

* **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Konto magazynu jest najwyższym poziomem obszaru nazw dostępu do obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, o ile ich całkowity rozmiar jest poniżej 100 TB.
* **Kontener:** Kontener zapewnia grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt blob**: Plik dowolnego typu i rozmiaru. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blok i strony obiektów blob. Większość plików to blokowe obiekty blob. Pojedynczy blokowy obiekt blob może mieć rozmiar do 200 GB. W tym samouczku użyto bloków obiektów blob. Obiekty BLOB strony, inny typ obiektu blob, może mieć rozmiar do 1 TB i są bardziej wydajne, gdy zakresy bajtów w pliku są często modyfikowane. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [Opis bloków obiektów blob, dołączanie obiektów blob i obiektów blob strony](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL:** obiekty blob można adresować przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Powyższy format ma zastosowanie do globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego w [witrynie Azure portal,](https://portal.azure.com) aby określić punkt końcowy adresu URL.)
  
    W powyższym `storageaccount` formacie reprezentuje nazwę konta `container_name` magazynu, reprezentuje nazwę kontenera i `blob_name` reprezentuje nazwę obiektu blob, odpowiednio. W nazwie kontenera można mieć wiele ścieżek oddzielonych **/** ukośnikiem do przodu, . Przykładowa nazwa kontenera używana w tym samouczku to **MyJob**, a **\_${BUILD ID}/${BUILD\_NUMBER}** został użyty dla wspólnej ścieżki wirtualnej, co spowodowało, że obiekt blob ma adres URL następującego formularza:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli wystąpią jakiekolwiek błędy z wtyczkami jenkins, zgładź problem w [JIRA usługi Jenkins](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/Jenkins/)