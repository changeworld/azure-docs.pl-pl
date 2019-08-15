---
title: Korzystanie z usługi Azure Storage z rozwiązaniem ciągłej integracji Jenkins
description: W tym samouczku pokazano, jak używać usługi Azure Blob Service jako repozytorium dla artefaktów kompilacji utworzonych przez rozwiązanie ciągłej integracji Jenkins.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 08/13/2019
ms.subservice: common
ms.openlocfilehash: dc62696700a5c34c28f5f8c4f347dbb4c5183cab
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986543"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem ciągłej integracji Jenkins

W tym artykule pokazano, jak używać magazynu obiektów BLOB jako repozytorium artefaktów kompilacji utworzonych przez rozwiązanie Jenkins ciągłej integracji (CI) lub jako źródło plików do pobrania, które mają być używane w procesie kompilacji. Jednym z scenariuszy, w których znalezienie tego rozwiązania jest przydatne, jest kodowanie w środowisku deweloperskim Agile (przy użyciu języka Java lub innych języków), kompilacje są uruchamiane na podstawie ciągłej integracji i potrzebne jest repozytorium dla artefaktów kompilacji, dzięki czemu Możesz na przykład udostępnić je innym członkom organizacji, Twoim klientom lub obsłudze archiwum. Innym scenariuszem jest to, że zadanie kompilacji wymaga innych plików, na przykład zależności do pobrania w ramach danych wejściowych kompilacji.

W tym samouczku będziesz używać wtyczki usługi Azure Storage dla Jenkins CI udostępnionej przez firmę Microsoft.

## <a name="jenkins-overview"></a>Jenkins — Omówienie
Jenkins zapewnia ciągłą integrację projektu oprogramowania przez umożliwienie deweloperom łatwej integracji zmian w kodzie i tworzenia kompilacji automatycznie i często, zwiększając jednocześnie produktywność deweloperów. Kompilacje są w wersji i artefakty kompilacji można przekazać do różnych repozytoriów. W tym artykule przedstawiono sposób korzystania z usługi Azure Blob Storage jako repozytorium artefaktów kompilacji. Pokazuje również, jak pobrać zależności z usługi Azure Blob Storage.

Więcej informacji na temat Jenkins można znaleźć pod adresem [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Zalety korzystania z Blob service
Korzyści wynikające z używania Blob service do hostowania artefaktów kompilacji Agile obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub możliwe do pobrania zależności.
* Wydajność, gdy rozwiązanie Jenkins CI przekazuje artefakty kompilacji.
* Wydajność, gdy klienci i partnerzy pobierają artefakty kompilacji.
* Kontrola nad zasadami dostępu użytkowników przy użyciu opcji dostępu anonimowego, dostępu do sygnatur dostępu współdzielonego opartego na wygaśnięciu, dostępu prywatnego itp.

## <a name="prerequisites"></a>Wymagania wstępne
* Rozwiązanie Jenkins ciągłej integracji.
  
    Jeśli obecnie nie masz rozwiązania CI Jenkins, możesz uruchomić rozwiązanie Jenkins CI, korzystając z następującej techniki:
  
  1. Na maszynie z obsługą języka Java Pobierz Jenkins. War z <https://jenkins-ci.org>.
  2. W wierszu polecenia otwartym w folderze, który zawiera Jenkins. War, uruchom polecenie:
     
      `java -jar jenkins.war`

  3. W przeglądarce Otwórz `http://localhost:8080/` program, aby otworzyć pulpit nawigacyjny Jenkins, który zostanie użyty do zainstalowania i skonfigurowania wtyczki usługi Azure Storage.
     
      W przypadku typowego rozwiązania CI Jenkins można skonfigurować do uruchamiania jako usługa, a w wierszu polecenia musi być wystarczająca wartość Jenkins War.
* Konto platformy Azure. Konto platformy Azure można zarejestrować pod adresem <https://www.azure.com>.
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz utworzyć je, wykonując czynności opisane w sekcji [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).
* Znajomość rozwiązania Jenkins CI jest zalecana, ale nie jest wymagana, ponieważ Poniższa zawartość będzie używać przykładu podstawowego do wyświetlania kroków potrzebnych podczas korzystania z Blob service jako repozytorium dla artefaktów kompilacji Jenkins CI.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jak używać Blob service z Jenkinsą CI
Aby użyć Blob service z Jenkins, należy zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do korzystania z konta magazynu, a następnie utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu. Te kroki opisano w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. Na pulpicie nawigacyjnym Jenkins wybierz pozycję **Zarządzaj Jenkins**.
2. Na stronie **Zarządzanie Jenkins** wybierz pozycję **Zarządzaj wtyczkami**.
3. Wybierz kartę **Available** (Dostępne).
4. W sekcji **operacje przekazywania artefaktów** Sprawdź **Microsoft Azure Storage wtyczka**.
5. Wybierz opcję **Zainstaluj bez ponownego uruchomienia** lub **Pobierz teraz i zainstaluj po ponownym uruchomieniu**.
6. Uruchom ponownie Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak skonfigurować wtyczkę usługi Azure Storage pod kątem korzystania z konta magazynu
1. Na pulpicie nawigacyjnym Jenkins wybierz pozycję **Zarządzaj Jenkins**.
2. Na stronie **Zarządzanie Jenkins** wybierz pozycję **Konfiguruj system**.
3. W sekcji **Konfiguracja konta Microsoft Azure Storage** :
   1. Wprowadź nazwę konta magazynu, którą można uzyskać z [Azure Portal](https://portal.azure.com).
   2. Wprowadź klucz konta magazynu, który można również uzyskać z [Azure Portal](https://portal.azure.com).
   3. Jeśli używasz globalnej chmury platformy Azure, użyj wartości domyślnej dla **adresu URL punktu końcowego usługi BLOB Service** . Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego określonego w [Azure Portal](https://portal.azure.com) dla konta magazynu. 
   4. Wybierz pozycję **Weryfikuj poświadczenia magazynu** , aby zweryfikować konto magazynu. 
   5. Obowiązkowe Jeśli masz dodatkowe konta magazynu, które chcesz udostępnić Jenkins CI, wybierz pozycję **Dodaj więcej kont magazynu**.
   6. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu
W celach informacyjnych należy najpierw utworzyć zadanie, które spowoduje utworzenie kilku plików, a następnie dodanie go do akcji po kompilacji w celu przekazania plików na konto magazynu.

1. Na pulpicie nawigacyjnym Jenkins wybierz pozycję **nowy element**.
2. Nazwij **MyJob**zadania, wybierz opcję **Kompiluj projekt oprogramowania o dowolnym stylu**, a następnie wybierz przycisk **OK**.
3. W sekcji **kompilacja** w obszarze Konfiguracja zadania wybierz pozycję **Dodaj krok kompilacji** i wybierz **polecenie Uruchom zadanie wsadowe systemu Windows**.
4. W **poleceniu**Użyj następujących poleceń:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. W sekcji **Akcje po kompilacji** w obszarze Konfiguracja zadania wybierz pozycję **Dodaj akcję po kompilacji** i wybierz pozycję **Przekaż artefakty do usługi Azure Blob Storage**.
6. W polu **nazwa konta magazynu**wybierz konto magazynu, które ma być używane.
7. W polu **nazwa kontenera**Określ nazwę kontenera. (Kontener zostanie utworzony, jeśli jeszcze nie istnieje, gdy artefakty kompilacji są przekazywane). Możesz użyć zmiennych środowiskowych, więc w tym przykładzie wprowadź `${JOB_NAME}` jako nazwę kontenera.
   
    **Porada**
   
    Poniżej sekcji **polecenie** , w której wprowadzono skrypt do **wykonywania polecenia Windows Batch** jest łączem do zmiennych środowiskowych rozpoznawanych przez Jenkins. Wybierz ten link, aby poznać nazwy zmiennych środowiskowych i opisy. Zmienne środowiskowe, które zawierają znaki specjalne, takie jak zmienna środowiskowa **BUILD_URL** , nie mogą być nazwami kontenerów ani wspólną ścieżką wirtualną.
8. Wybierz opcję **Utwórz nowy kontener jako publiczny domyślnie** dla tego przykładu. (Jeśli chcesz użyć prywatnego kontenera, musisz utworzyć sygnaturę dostępu współdzielonego w celu zezwolenia na dostęp, która wykracza poza zakres tego artykułu. Więcej informacji na temat sygnatur dostępu współdzielonego można uzyskać przy [użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
9. Obowiązkowe Wybierz pozycję **Oczyść kontener przed** przekazaniem, jeśli chcesz, aby kontener został usunięty z zawartości przed przekazaniem artefaktów kompilacji (pozostaw to pole niezaznaczone, jeśli nie chcesz czyścić zawartości kontenera).
10. Aby uzyskać **listę artefaktów do przekazania**, `text/*.txt`wprowadź.
11. Dla **typowej ścieżki wirtualnej dla przekazanych artefaktów**, na potrzeby tego samouczka wprowadź `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.
13. Na pulpicie nawigacyjnym Jenkins wybierz opcję **Kompiluj teraz** , aby uruchomić **MyJob**. Przejrzyj dane wyjściowe konsoli dla stanu. Komunikaty o stanie usługi Azure Storage zostaną uwzględnione w danych wyjściowych konsoli, gdy akcja po kompilacji zacznie przekazywać artefakty kompilacji.
14. Po pomyślnym zakończeniu zadania można przeanalizować artefakty kompilacji, otwierając publiczny obiekt BLOB.
    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Wybierz pozycję **Magazyn**.
    3. Wybierz nazwę konta magazynu, która została użyta dla Jenkins.
    4. Wybierz **kontenery**.
    5. Wybierz kontener o nazwie **myjob**, który jest małymi wersjami nazwy zadania przypisanego podczas tworzenia zadania Jenkins. Nazwy kontenerów i nazw obiektów BLOB są małymi literami (i rozróżniana wielkość liter) w usłudze Azure Storage. Na liście obiektów BLOB kontenera o nazwie **myjob**powinna być widoczna wartość **Hello. txt** i **Date. txt**. Skopiuj adres URL dla każdego z tych elementów i otwórz go w przeglądarce. Zobaczysz plik tekstowy, który został przekazany jako artefakt kompilacji.

Dla każdego zadania można utworzyć tylko jedną akcję po kompilacji, która przekazuje artefakty do magazynu obiektów blob platformy Azure. Pojedyncza Akcja po kompilacji w celu przekazania artefaktów do usługi Azure Blob Storage może określać różne pliki (w tym symbole wieloznaczne) i ścieżki do plików znajdujących się na **liście artefaktów do przekazania** przy użyciu średnika jako separatora. Na przykład jeśli kompilacja Jenkins tworzy pliki JAR i TXT w folderze **kompilacji** obszaru roboczego i chcesz przekazać oba do usługi Azure Blob Storage, użyj następującej wartości, aby uzyskać **listę artefaktów do przekazania** : `build/\*.jar;build/\*.txt`. Aby określić ścieżkę do użycia w nazwie obiektu BLOB, można również użyć składni podwójnego dwukropka. Na przykład jeśli chcesz, aby JARs został przekazany przy użyciu plików **binarnych** w ścieżce obiektu BLOB i pliki txt do przekazania przy użyciu **powiadomień** w ścieżce obiektu BLOB, użyj następującej wartości dla **listy artefaktów do przekazania** : `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji pobierany z usługi Azure Blob Storage
Poniższe kroki pokazują, jak skonfigurować krok kompilacji do pobierania elementów z usługi Azure Blob Storage, co jest przydatne, jeśli chcesz dołączyć elementy do kompilacji. Przykładem użycia tego wzorca jest JARs, który można zachować w usłudze Azure Blob Storage.

1. W sekcji **kompilacja** w obszarze Konfiguracja zadania wybierz pozycję **Dodaj krok kompilacji** , a następnie wybierz pozycję **Pobierz z usługi Azure Blob Storage**.
2. W polu **nazwa konta magazynu**wybierz konto magazynu, które ma być używane.
3. W polu **nazwa kontenera**Określ nazwę kontenera zawierającego obiekty blob, które chcesz pobrać. Można używać zmiennych środowiskowych.
4. W polu **Nazwa obiektu BLOB**Określ nazwę obiektu BLOB. Można używać zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu początkowych liter nazwy obiektu BLOB. Na przykład **projekt\\** * będzie określać wszystkie obiekty blob, których nazwy zaczynają się od **Project**.
5. Obowiązkowe W polu **ścieżka do pobierania**określ ścieżkę na maszynie Jenkins, w której chcesz pobrać pliki z usługi Azure Blob Storage. Można również użyć zmiennych środowiskowych. (Jeśli nie podasz wartości **ścieżki pobierania**, pliki z usługi Azure Blob Storage zostaną pobrane do obszaru roboczego zadania).

Jeśli masz dodatkowe elementy, które chcesz pobrać z usługi Azure Blob Storage, możesz utworzyć dodatkowe kroki kompilacji.

Po uruchomieniu kompilacji można sprawdzić dane wyjściowe konsoli historia kompilacji lub poszukać lokalizacji pobierania, aby sprawdzić, czy obiekty blob, których oczekiwano, zostały pobrane pomyślnie.  

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez Blob service
Ta sekcja zawiera omówienie składników Blob service.

* **Konto magazynu**: Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Konto magazynu to najwyższy poziom przestrzeni nazw do uzyskiwania dostępu do obiektów BLOB. Konto może zawierać nieograniczoną liczbę kontenerów, o ile ich łączny rozmiar przekracza 100 TB.
* **Kontener**: Kontener zawiera grupowanie zestawu obiektów BLOB. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt BLOB**: Plik o dowolnym typie i rozmiarze. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blokowe i stronicowe obiekty blob. Większość plików to blokowe obiekty blob. Pojedynczy blokowy obiekt BLOB może mieć rozmiar do 200 GB. W tym samouczku są stosowane blokowe obiekty blob. Stronicowe obiekty blob, inne typy obiektów blob, mogą mieć rozmiar do 1 TB i są bardziej wydajne, gdy często są modyfikowane zakresy bajtów w pliku. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [Opis blokowych obiektów blob, dołączania obiektów blob i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL**: Obiekty blob są adresowane przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Powyższy format dotyczy globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego w [Azure Portal](https://portal.azure.com) , aby określić punkt końcowy adresu URL.)
  
    W powyższym `storageaccount` formacie reprezentuje nazwę konta magazynu, `container_name` reprezentuje nazwę kontenera i `blob_name` reprezentuje odpowiednio nazwę obiektu BLOB. W obrębie nazwy kontenera można mieć wiele ścieżek oddzielonych ukośnikiem **/** . Przykładowa nazwa kontenera użyta w tym samouczku została MyJoba, a **$\_{Build ID}/\_$ {Number Build}** została użyta dla wspólnej ścieżki wirtualnej, co spowodowało, że obiekt BLOB ma adres URL w następującej postaci:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki
* [Poznaj Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).
