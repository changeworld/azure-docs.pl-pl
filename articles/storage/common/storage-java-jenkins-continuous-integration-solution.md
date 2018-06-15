---
title: Użycie magazynu Azure z rozwiązaniem ciągłej integracji Wpięć | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak używać usługi obiektów blob platformy Azure jako repozytorium dla kompilacji artefakty utworzone przez rozwiązanie Wpięć ciągłej integracji.
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: f4e5ca75-f6cb-4f74-981b-2aa06bb8de45
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: 174ac449e803ed5327468a38ea7264cb9923a877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874136"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem ciągłej integracji Jenkins
## <a name="overview"></a>Omówienie
Poniższe informacje pokazuje, jak używać magazynu obiektów Blob jako repozytorium artefaktów kompilacji utworzone przez rozwiązanie Wpięć ciągłej integracji (CI) lub jako źródło do pobrania plików do użycia w procesie kompilacji. Jest jednego ze scenariuszy, w którym użytkownik może to być przydatne, gdy jest kodowania w środowisku elastyczne programowanie (przy użyciu języka Java lub innych języków), kompilacje działają na podstawie ciągłej integracji i należy repozytorium artefaktów z kompilacji, dzięki czemu można, na przykład udostępniać je z innymi elementami członkowskimi organizacji klienta, lub Obsługa archiwum. Inny scenariusz jest, gdy sam zadania kompilacji wymaga innych plików, na przykład zależności, aby pobrać jako część kompilacji danych wejściowych.

W tym samouczku używana będzie wtyczki magazynu Azure dla Wpięć elementu CI udostępnione przez firmę Microsoft.

## <a name="overview-of-jenkins"></a>Omówienie Wpięć
Wpięć włącza ciągłej integracji projektu oprogramowania zezwalając deweloperom łatwo zintegrować ich zmiany kodu i kompilacji tworzą automatycznie i często, co zwiększa produktywność deweloperów. Kompilacje są kontrolą wersji, a do różnych repozytoria można przekazać artefaktów kompilacji. W tym temacie będzie pokazują, jak używać magazynu obiektów blob platformy Azure jako repozytorium artefaktów kompilacji. Zostaną wyświetlone również sposób pobierania zależności z magazynu obiektów blob platformy Azure.

Więcej informacji na temat Wpięć można znaleźć w folderze [spełnia Wpięć](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Korzyści wynikające z korzystania z usługi Blob
Korzystanie z usługi Blob do obsługi sieci artefaktów kompilacji elastyczne programowanie zalety:

* Wysoka dostępność artefaktów kompilacji i/lub zależności do pobrania.
* Wydajność rozwiązania CI Wpięć przekazuje użytkownika artefaktów kompilacji.
* Wydajność, gdy klienci i partnerzy pobrać artefaktów z kompilacji.
* Kontrolę nad zasad dostępu, wybór między anonimowego dostępu, dostęp sygnatury dostępu współdzielonego opartych na wygaśnięcia, prywatne, dostępu itd.

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebne są następujące polecenie, aby korzystać z usługi Blob wraz z rozwiązaniem Wpięć CI:

* Rozwiązanie Wpięć ciągłej integracji.
  
    Jeśli obecnie nie ma rozwiązania CI Wpięć, można uruchomić rozwiązanie CI Wpięć przy użyciu zrobić w następujący sposób:
  
  1. Na komputerze z obsługą języka Java, Pobierz jenkins.war z <http://jenkins-ci.org>.
  2. W wierszu polecenia, który jest otwarty do folderu, który zawiera jenkins.war Uruchom polecenie:
     
      `java -jar jenkins.war`

  3. W przeglądarce otwórz `http://localhost:8080/`. Spowoduje to otwarcie Wpięć pulpitu nawigacyjnego, który będzie używany do zainstalowania i skonfigurowania wtyczki usługi Azure Storage.
     
      Podczas gdy typowe rozwiązania Wpięć elementu konfiguracji należy skonfigurować do uruchamiania jako usługa, uruchomiona war Wpięć w wierszu polecenia będą wystarczające do celów tego samouczka.
* Konto platformy Azure. Można założyć konto platformy Azure w usłudze <http://www.azure.com>.
* Konto usługi Azure Storage. Jeśli nie masz już konto magazynu, można utworzyć przy użyciu kroki opisane w temacie [Utwórz konto magazynu](../common/storage-create-storage-account.md#create-a-storage-account).
* Znajomość rozwiązania CI Wpięć jest zalecane, ale nie jest to wymagane, zgodnie z następującą zawartość użyje prosty przykład do pokazania kroki niezbędne podczas korzystania z usługi Blob jako repozytorium dla Wpięć elementu CI tworzenie artefaktów.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jak używać usługi obiektów Blob z Wpięć elementu konfiguracji
Do korzystania z usługi obiektów Blob z Wpięć, musisz zainstalować dodatek usługi Azure Storage, skonfigurować wtyczkę do używania konta magazynu, a następnie Utwórz akcję po kompilacji, która przekazuje użytkownika artefaktów kompilacji do konta magazynu. Te kroki są opisane w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować dodatek usługi Azure Storage
1. W pulpicie nawigacyjnym Wpięć kliknij **Zarządzanie Wpięć**.
2. W **Zarządzanie Wpięć** kliknij przycisk **Zarządzanie wtyczkami**.
3. Kliknij przycisk **dostępne** kartę.
4. W **Uploaders artefaktu** sekcji wyboru **dodatek Microsoft Azure Storage**.
5. Kliknij opcję **zainstalować bez ponownego uruchomienia** lub **teraz pobrać i zainstalować po ponownym uruchomieniu**.
6. Uruchom ponownie Wpięć.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak skonfigurować wtyczki usługi Azure Storage do używania konta magazynu
1. W pulpicie nawigacyjnym Wpięć kliknij **Zarządzanie Wpięć**.
2. W **Zarządzanie Wpięć** kliknij przycisk **skonfigurować System**.
3. W **konfiguracji konta magazynu Azure Microsoft** sekcji:
   1. Wprowadź nazwę konta magazynu, który można uzyskać z [Azure Portal](https://portal.azure.com).
   2. Wprowadź klucz konta magazynu również u: [Azure Portal](https://portal.azure.com).
   3. Użyj wartości domyślnej dla **adres URL punktu końcowego usługi Blob** korzystania z chmury publicznej Azure. Jeśli używasz innej chmurze platformy Azure, użyj punktu końcowego jak określono w [Azure Portal](https://portal.azure.com) dla konta magazynu. 
   4. Kliknij przycisk **sprawdzanie poprawności poświadczeń magazynu** można sprawdzić poprawności konta magazynu. 
   5. [Opcjonalnie] Jeśli masz dodatkowych kont magazynu, które mają udostępnione CI z Wpięć, kliknij przycisk **dodać więcej kont magazynu**.
   6. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcję po kompilacji, która przekazuje użytkownika artefaktów kompilacji do konta magazynu
Do celów instrukcji najpierw zostanie należy utworzyć zadanie, które będzie utworzyć kilka plików, a następnie dodaj w akcji mające miejsce po kompilacji, aby przekazać pliki do konta magazynu.

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij **New Item** (Nowy element).
2. Nazwa zadania **MyJob**, kliknij przycisk **kompilowania projektu oprogramowania wolne stylu**, a następnie kliknij przycisk **OK**.
3. W **kompilacji** sekcji konfiguracji zadania, kliknij przycisk **kroku kompilacji Dodaj** i wybierz polecenie **wykonywanie wsadowe systemu Windows polecenie**.
4. W **polecenia**, użyj następujących poleceń:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. W **działania mające miejsce po kompilacji** sekcji konfiguracji zadania, kliknij przycisk **dodać akcję postkompilacyjnego** i wybierz polecenie **przekazać artefakty do magazynu obiektów Blob Azure**.
6. Aby uzyskać **nazwy konta magazynu**, wybierz konto magazynu do użycia.
7. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera. (Kontener zostanie utworzony, jeśli go jeszcze nie istnieje, gdy są przekazywane artefaktów kompilacji.) Można używać zmiennych środowiskowych, dlatego w tym przykładzie wprowadź **${JOB_NAME}** jako nazwa kontenera.
   
    **Porada**
   
    Poniżej **polecenia** sekcji, w którym wprowadzono skryptu dla **wykonywanie wsadowe systemu Windows polecenie** jest łącze do zmiennych środowiskowych rozpoznaje Wpięć. Kliknij łącze aby dowiedzieć się nazwy zmiennych środowiskowych i opisy. Należy pamiętać, że zmienne środowiskowe, które zawierają specjalne znaki, takie jak **BUILD_URL** zmiennej środowiskowej, nie są dozwolone jako nazwa kontenera lub wspólnej ścieżki wirtualnej.
8. Kliknij przycisk **Upublicznij domyślnie nowy kontener** w tym przykładzie. (Jeśli chcesz użyć prywatnych kontenera, musisz utworzyć sygnatury dostępu współdzielonego, aby zezwolić na dostęp. Która wykracza poza zakres tego tematu. Dowiedz się więcej na temat sygnatur dostępu współdzielonego w [za pomocą sygnatur dostępu do udostępnionych (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcjonalnie] Kliknij przycisk **czystą kontenera przed przekazaniem** Jeśli chcesz kontenera do wyczyszczenia zawartości przed artefaktów kompilacji są przekazywane (Pozostaw zaznaczenie opcji, jeśli nie chcesz wyczyścić zawartość kontenera).
10. Dla **listy artefakty do przekazania**, wprowadź  **tekstu /*.txt**.
11. Dla **wspólnej ścieżki wirtualnej przekazane artefaktów**dla celów tego samouczka, wprowadź **${kompilacji\_identyfikator} / ${kompilacji\_numer}**.
12. Kliknij przycisk **zapisać** Aby zapisać ustawienia.
13. Na pulpicie nawigacyjnym Wpięć kliknij **kompilacji teraz** do uruchomienia **MyJob**. Sprawdź dane wyjściowe konsoli stanu. Komunikaty o stanie dla magazynu Azure będą uwzględniane w danych wyjściowych konsoli po uruchomieniu akcji postkompilacyjnego do przekazania artefaktów kompilacji.
14. Po pomyślnym ukończeniu zadania należy zbadać artefaktów kompilacji przez otwarcie publicznego obiektu blob.
    1. Zaloguj się do [portalu Azure](https://portal.azure.com).
    2. Kliknij przycisk **magazynu**.
    3. Kliknij nazwę konta magazynu, który był używany przez Wpięć.
    4. Kliknij przycisk **kontenery**.
    5. Kliknij kontener o nazwie **myjob**, małe litery wersji przypisany podczas tworzenia zadania Wpięć nazwy zadania. Nazwa kontenera i nazwy obiektów blob są małe litery (a z uwzględnieniem wielkości liter) w magazynie Azure. Na liście obiektów blob do kontenera o nazwie **myjob** powinna zostać wyświetlona **hello.txt** i **date.txt**. Skopiuj adres URL dla dowolnego z tych elementów, a następnie otwórz go w przeglądarce. Zobaczysz pliku tekstowego, który został przekazany jako artefaktów kompilacji.

Można utworzyć tylko jedną akcję mające miejsce po kompilacji, która przekazuje artefakty do magazynu obiektów blob platformy Azure na zadanie. Należy pamiętać, że jednej akcji postkompilacyjnego do przekazania do magazynu obiektów blob platformy Azure artefakty można określić różnych plików (w tym symboli wieloznacznych) i ścieżki do plików w **listy artefakty do przekazania** przy użyciu średnika jako separatora. Na przykład jeśli kompilacji z Wpięć tworzy pliki JAR i pliki TXT w swoim obszarze roboczym **kompilacji** folderu i chcesz przekazać zarówno do magazynu obiektów blob platformy Azure, użyj następujących **listy artefakty do przekazania** wartość: **kompilacji /\*JAR; kompilacji /\*.txt**. Aby określić ścieżkę do użycia w nazwie obiektu blob umożliwia także podwójnego dwukropka składni. Na przykład, jeśli chcesz słoików, aby zostać przekazane za pomocą **plików binarnych** w ścieżce obiektu blob i pliki TXT, aby zostać przekazane za pomocą **powiadomienia** w ścieżce obiektu blob, użyj następujących **listy artefakty do przekazania** wartość: **kompilacji /\*. jar::binaries; kompilacji /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć kroku kompilacji, który pobiera z magazynu obiektów blob platformy Azure
Poniższe kroki pokazują, jak skonfigurować kroku kompilacji pobierania elementów z magazynu obiektów blob platformy Azure. Powinien to być przydatne, jeśli chcesz uwzględnić elementów w kompilacji, na przykład słoików zachowujących na platformie Azure magazynu obiektów blob.

1. W **kompilacji** sekcji konfiguracji zadania, kliknij przycisk **kroku kompilacji Dodaj** i wybierz polecenie **pobrać z magazynu obiektów Blob Azure**.
2. Aby uzyskać **nazwy konta magazynu**, wybierz konto magazynu do użycia.
3. Aby uzyskać **nazwa kontenera**, określ nazwę kontenera, który zawiera obiekty BLOB do pobrania. Można używać zmiennych środowiskowych.
4. Aby uzyskać **nazwa obiektu Blob**, określ nazwę obiektu blob. Można używać zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu znakami nazwa obiektu blob. Na przykład **projektu\***  określić wszystkie obiekty BLOB, których nazwy rozpoczynają się od **projektu**.
5. [Opcjonalnie] Aby uzyskać **ścieżkę pobierania**, określ ścieżkę na maszynie Wpięć, którym chcesz pobrać pliki z magazynu obiektów blob platformy Azure. Można także zmiennych środowiskowych. (Jeśli nie zostanie określona wartość **ścieżkę pobierania**, pliki z magazynu obiektów blob platformy Azure zostanie pobrana do obszaru roboczego zadania.)

Jeśli masz dodatkowe elementy, które ma zostać pobrana z magazynu obiektów blob platformy Azure, można utworzyć kompilacji dodatkowe kroki.

Po uruchomieniu kompilacji, sprawdź dane wyjściowe konsoli historii kompilacji, lub przyjrzeć się do lokalizacji pobierania, aby zobaczyć, czy obiekty BLOB, które miały zostały pomyślnie pobrane.  

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez usługę obiektu Blob
Poniżej znajdują się omówienie składników usługi Blob.

* **Konto magazynu**: cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Jest to najwyższy poziom przestrzeni nazw do uzyskiwania dostępu do obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, tak długo, jak ich łączny rozmiar wynosi poniżej 100TB.
* **Kontener**: kontener zawiera grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **Obiekt blob**: typ i rozmiar pliku. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blokowe i stronicowe obiekty BLOB. Większość plików są blokowych obiektów blob. Pojedynczy blokowy obiekt blob może być maksymalnie 200GB. W tym samouczku korzysta z blokowych obiektów blob. Stronicowe obiekty BLOB innego typu obiektu blob, może być maksymalnie do 1TB, rozmiar i są bardziej wydajne, gdy zakresy bajtów w pliku są często modyfikowane. Aby uzyskać więcej informacji dotyczących obiektów blob, zobacz [opis blokowych obiektów blob, Dołącz obiektów blob i stronicowe obiekty BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL**: obiekty BLOB mają następujący format adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Format powyżej ma zastosowanie do chmury publicznej Azure. Jeśli używasz innej chmurze platformy Azure, użyj punktu końcowego w [Azure Portal](https://portal.azure.com) Aby określić adres URL punktu końcowego.)
  
    W formacie powyżej `storageaccount` reprezentuje nazwę konta magazynu `container_name` reprezentuje nazwę Twojej kontenera i `blob_name` odpowiednio reprezentuje nazwę z obiektu blob. W ramach nazwa kontenera może mieć wiele ścieżek rozdzielonych ukośnikiem,  **/** . Przykładowa nazwa kontenera w tym samouczku został **MyJob**, i **${kompilacji\_identyfikator} / ${kompilacji\_numer}** użyto wspólnej ścieżki wirtualnej, co w obiekcie blob o adres URL mają następującą postać:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Następne kroki
* [Spełnia Wpięć](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Magazyn Azure SDK dla języka Java](https://github.com/azure/azure-storage-java)
* [Odwołanie do zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).