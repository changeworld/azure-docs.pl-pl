---
title: Azure ML Workbench informacje o wersji dla przebiegu 2 grudnia 2017 r.
description: "Ten dokument zawiera szczegóły aktualizacje dla wersji przebiegu 2 uczenie Maszynowe Azure"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-2---december-2017"></a>Przebieg 2 — grudzień 2017 

#### <a name="version-number-01171115263"></a>Numer wersji: 0.1.1711.15263

>Poniżej przedstawiono sposób [Znajdź numer wersji](known-issues-and-troubleshooting-guide.md).

Trzeci aktualizacja Azure Machine Learning Workbench — Zapraszamy! Ta aktualizacja zawiera ulepszenia w aplikacji workbench, interfejsu wiersza polecenia (CLI) i usługi zaplecza. Dziękujemy wysyłania uśmiechy i frowns. Wiele z następujących aktualizacji są wykonywane jako bezpośrednie wyniki opinii użytkowników. 

## <a name="notable-new-features"></a>Ważne funkcje
- [Obsługa programu SQL Server i bazy danych SQL Azure jako źródła danych](data-prep-appendix2-supported-data-sources.md#types) 
- [Głębokie uczenie na Spark z obsługą procesora GPU za pomocą MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Wszystkie kontenery AML są zgodne z urządzenia Azure IoT brzegowe po wdrożeniu (żadne dodatkowe czynności wymagane)](http://aka.ms/aml-iot-edge-blog)
- Zarejestrowanego modelu listy i szczegółów widoki dostępne portalu Azure
- Uzyskiwanie dostępu do celów obliczeń przy użyciu uwierzytelniania opartego na kluczach SSH oprócz dostępu opartego na nazwę użytkownika/hasło. 
- Nowy wzorzec inspektora częstotliwość danych przygotowywanie środowiska. 

## <a name="detailed-updates"></a>Szczegółowe aktualizacji
Poniżej znajduje się lista szczegółowe aktualizacji w obszarze każdego składnika uczenie maszynowe Azure w tym przebiegu.

### <a name="installer"></a>Instalator
- Instalator może self aktualizacji, który usterek poprawki i nowe funkcje mogą być obsługiwane bez konieczności ponownie ją zainstaluj użytkownika

### <a name="workbench-authentication"></a>Workbench uwierzytelniania
- Wiele poprawek do uwierzytelniania systemu. Prosimy o kontakt Jeśli nadal występują problemy dotyczące logowania.
- Zmiany interfejsu użytkownika, które ułatwiają znajdowanie ustawienia Menedżera serwera Proxy.

### <a name="workbench"></a>Workbench
- Widok tylko do odczytu pliku ma teraz światła niebieskie tło
- Przycisk Edytuj przeniesiony z prawej strony, aby był bardziej wykrywalny.
- formaty plików "ipynb", "dprep" i "dsource" teraz mogą być renderowane w formacie tekstowym pierwotnych
- Workbench ma teraz nowe środowisko edytowania, który przeprowadzi użytkowników do edycji skryptów i użyj Workbench tylko do typów plików, które mają bogate środowisko edycji (na przykład komputery przenośne źródeł danych, pakiety przygotowywania danych) Edytuj przy użyciu zewnętrznego IDE
- Podczas ładowania listy obszarów roboczych i projekty, do których użytkownik ma dostęp jest znacznie szybsze teraz

### <a name="data-preparation"></a>Przygotowywanie danych 
- Wzorzec Inspektor częstotliwość przeszukiwarki ciągu w kolumnie. Można również filtrować dane przy użyciu tych wzorców. To pokazuje widok podobny do Inspektora wartość liczby. Różnica polega na tym, że wzorzec częstotliwość zawiera informacje o liczbie unikatowych wzorców danych, a nie liczby unikatowych danych. Można również filtrować przychodzący lub wychodzący wszystkie wiersze, które pasują do wzorca niektórych.

![Obraz inspektora częstotliwość wzorzec na numer produktu](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Ulepszenia wydajności podczas rekomendowania przypadków krawędzi, aby przejrzeć w transformacji "pochodzi kolumny przykładzie"

- [Obsługa programu SQL Server i bazy danych SQL Azure jako źródła danych](data-prep-appendix2-supported-data-sources.md#types) 

![Obraz tworzenia nowego źródła danych serwera SQL](media/release-notes-sprint-2/sql-server-data-source.png)

- Włączone widok "w skrócie" liczby wierszy i kolumn

![Obraz wiersza liczba kolumn w prolongaty](media/release-notes-sprint-2/row-col-count.png)

- Przygotowanie bazy danych jest włączona we wszystkich kontekstach obliczeń
- Źródła danych, które używają bazy danych programu SQL Server są włączone we wszystkich kontekstach obliczeń
- Dane przygotowywanie siatki, które kolumny można filtrować według typu danych
- Rozwiązany problem z konwertowaniem wiele kolumn do daty
- Rozwiązany problem ten użytkownik może zaznaczenie kolumny wyjściowej jako źródło w pochodzi kolumny według przykładu użytkownik zmienił nazwy kolumny wyjściowej w trybie zaawansowanym.

### <a name="job-execution"></a>Wykonanie zadania
Teraz można tworzyć i dostęp do klastra lub remotedocker cel obliczeń typu przy użyciu uwierzytelniania opartego na kluczach SSH wykonaj następujące czynności:
- Dołączanie elementu docelowego obliczeń przy użyciu następującego polecenia w interfejsu wiersza polecenia

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] Opcja -k (lub--użyj — uczenie maszynowe Azure-— klucz ssh), w poleceniu określa generowanie i używanie klucza SSH.

- Azure ML Workbench wygenerowania klucza publicznego i wyjściowy, który w konsoli. Zaloguj się do obiektu docelowego obliczeń przy użyciu tej samej nazwy użytkownika i Dołącz plik ~/.ssh/authorized_keys kluczem publicznym.

- Można przygotować ten element docelowy obliczeń i użyć jej do wykonania i Workbench uczenia Maszynowego Azure będzie używać tego klucza do uwierzytelniania.  

Aby uzyskać więcej informacji na temat tworzenia elementów docelowych obliczeń, zobacz [Konfigurowanie Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- Dodano obsługę [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia (CLI)
- Dodaje `az ml datasource create` polecenie umożliwia utworzenie pliku źródła danych z wiersza polecenia

### <a name="model-management-and-operationalization"></a>Zarządzanie modelami i Operationalization
- [Wszystkie kontenery AML są zgodne z urządzenia Azure IoT brzegowe podczas operationalized (żadne dodatkowe czynności wymagane)](http://aka.ms/aml-iot-edge-blog) 
- Ulepszenia komunikatów o błędach w o16n interfejsu wiersza polecenia
- Poprawki błędów w portalu zarządzania modelu środowiska użytkownika  
- Wielkość liter spójne litery model zarządzania atrybutów na stronie szczegółów
- Ocenianie limitu czasu połączenia w czasie rzeczywistym ustawioną 60 sekund
- Zarejestrowanego modelu listy i szczegółów widoków dostępne w portalu Azure

![Szczegóły modelu w portalu](media/release-notes-sprint-2/model-list.jpg)

![Omówienie modelu w portalu](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Bezpośrednie Learning na Spark z [Obsługa procesora GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Obsługa szablonów usługi Resource Manager łatwe zasobu wdrożenia
- Obsługa ekosystem SparklyR
- [Integracja AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Przykładowych projektach
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) i [MMLSpark](https://github.com/Azure/mmlspark) przykłady zaktualizowane o nową wersję zestawu SDK usługi Azure ML

## <a name="breaking-changes"></a>FUNDAMENTALNE ZMIANY
- Poziom jest podwyższany `--type` przełącznika w `az ml computetarget attach` polecenie podrzędne. 

    - `az ml computetarget attach --type remotedocker` jest teraz `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` jest teraz `az ml computetarget attach cluster`
