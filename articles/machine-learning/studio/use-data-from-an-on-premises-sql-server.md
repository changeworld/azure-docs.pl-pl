---
title: Lokalny program SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Użyj danych z lokalnej bazy danych programu SQL Server do wykonywania zaawansowanej analizy za pomocą usługi Azure Machine Learning Studio (classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204186"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Wykonywanie analiz za pomocą usługi Azure Machine Learning Studio (klasycznej) przy użyciu lokalnej bazy danych programu SQL Server

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Często przedsiębiorstwa, które pracują z danymi lokalnymi, chcą korzystać ze skali i elastyczności chmury dla swoich obciążeń uczenia maszynowego. Nie chcą jednak zakłócać bieżących procesów biznesowych i przepływów pracy, przenosząc dane lokalne do chmury. Usługa Azure Machine Learning Studio (klasyczna) obsługuje teraz odczytywanie danych z lokalnej bazy danych programu SQL Server, a następnie szkolenie i ocenianie modelu za pomocą tych danych. Nie trzeba już ręcznie kopiować i synchronizować danych między chmurą a serwerem lokalnym. Zamiast tego moduł **Importuj dane** w usłudze Azure Machine Learning Studio (klasyczny) można teraz odczytać bezpośrednio z lokalnej bazy danych programu SQL Server dla zadań szkoleniowych i oceniania.

Ten artykuł zawiera omówienie sposobu wprowadzania danych serwera SQL w środowisku lokalnym do usługi Azure Machine Learning Studio (klasyczny). Zakłada się, że znasz pojęcia Studio (klasyczne), takie jak obszary robocze, moduły, zestawy danych, eksperymenty *itp.*

> [!NOTE]
> Ta funkcja nie jest dostępna w przypadku wolnych obszarów roboczych. Aby uzyskać więcej informacji na temat cen i warstw usługi Machine Learning, zobacz [Cennik usługi Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalowanie środowiska wykonawczego integracji z własnym hostem fabryki danych
Aby uzyskać dostęp do lokalnej bazy danych programu SQL Server w usłudze Azure Machine Learning Studio (klasyczna), należy pobrać i zainstalować własny hostowany środowisko uruchomieniowe integracji fabryki danych, wcześniej znane jako brama zarządzania danymi. Podczas konfigurowania połączenia w urzędzie machine learning studio (klasyczny), masz możliwość pobrania i zainstalowania integration runtime (IR) przy użyciu **pobierania i rejestrowania bramy danych** okno dialogowe opisane poniżej.


Urządzenie IR można również zainstalować z wyprzedzeniem, pobierając i uruchamiając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). MSI może również służyć do uaktualnienia istniejącego podczerwania do najnowszej wersji, ze wszystkimi ustawieniami zachowane.

Środowisko uruchomieniowe integracji z fabryką danych ma następujące wymagania wstępne:

* Integracja z fabryką danych wymaga 64-bitowego systemu operacyjnego z programem .NET Framework 4.6.1 lub nowszym.
* Obsługiwane wersje systemu operacyjnego Windows to Windows 10 , Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Zalecana konfiguracja komputera podczerwienia to co najmniej 2 GHz, 4-rdzeniowy procesor, 8 GB pamięci RAM i dysk 80 GB.
* Jeśli komputer-host hibernuje, iR nie odpowiada na żądania danych. W związku z tym należy skonfigurować odpowiedni plan zasilania na komputerze przed zainstalowaniem podczerwego. Jeśli urządzenie jest skonfigurowane do hibernacji, instalacja podczerwieni wyświetla komunikat.
* Ponieważ aktywność kopiowania występuje z określoną częstotliwością, użycie zasobów (CPU, pamięć) na komputerze również podąża za tym samym wzorcem z czasów szczytowych i bezczynnych. Wykorzystanie zasobów zależy również w dużej mierze od ilości przesyłanych danych. Gdy wiele zadań kopiowania są w toku, można obserwować użycie zasobów iść w górę w godzinach szczytu. Podczas gdy minimalna konfiguracja wymieniona powyżej jest technicznie wystarczająca, można mieć konfigurację z większą ilością zasobów niż minimalna konfiguracja w zależności od określonego obciążenia dla przenoszenia danych.

Podczas konfigurowania i używania środowiska wykonawczego integracji z własnym hostem danych należy wziąć pod uwagę następujące kwestie:

* Na jednym komputerze można zainstalować tylko jedno wystąpienie podczerwenia.
* Można użyć pojedynczego podczerwaskowego dla wielu lokalnych źródeł danych.
* Do tego samego lokalnego źródła danych można połączyć wiele irów na różnych komputerach.
* Konfigurowanie irs tylko dla jednego obszaru roboczego naraz. Obecnie nie można udostępniać w różnych obszarach roboczych.
* Można skonfigurować wiele irs dla jednego obszaru roboczego. Na przykład można użyć podczerwony, który jest połączony ze źródłami danych testowych podczas tworzenia i produkcji IR, gdy jesteś gotowy do operationalize.
* IR nie musi znajdować się na tym samym komputerze co źródło danych. Jednak przybliżanie się do źródła danych skraca czas na połączenie się bramy ze źródłem danych. Zaleca się zainstalowanie podczerwonego środowiska na komputerze, który różni się od tego, na którym znajduje się lokalne źródło danych, aby brama i źródło danych nie konkurować o zasoby.
* Jeśli masz już zainstalowaną podczerwórkę na komputerze obsługującym scenariusze usługi Power BI lub Azure Data Factory, zainstaluj oddzielne podczerwone podczerwone dla usługi Azure Machine Learning Studio (klasyczne) na innym komputerze.

  > [!NOTE]
  > Na tym samym komputerze nie można uruchomić samodzielnie hostowanego środowiska wykonawczego integracji fabryki danych i bramy usługi Power BI Gateway.
  >
  >
* Należy użyć środowiska uruchomieniowego integracji z własnym hostem fabryki danych dla usługi Azure Machine Learning Studio (klasycznej), nawet jeśli używasz usługi Azure ExpressRoute dla innych danych. Źródło danych należy traktować jako lokalne źródło danych (które jest za zaporą), nawet podczas korzystania z usługi ExpressRoute. Użyj środowiska uruchomieniowego integracji z fabryką danych, aby ustanowić łączność między uczeniem maszynowym a źródłem danych.

Szczegółowe informacje na temat wymagań wstępnych instalacji, kroków instalacji i wskazówek dotyczących rozwiązywania problemów można znaleźć w artykule [Środowisko wykonawcze integracji w fabryce danych](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dane transferu danych przychodzących z lokalnej bazy danych programu SQL Server do usługi Azure Machine Learning
W tym instruktażu skonfigurujesz środowisko uruchomieniowe integracji usługi Azure Data Factory Integration w obszarze roboczym usługi Azure Machine Learning, skonfigurujesz je, a następnie odczytujesz dane z lokalnej bazy danych programu SQL Server.

> [!TIP]
> Przed rozpoczęciem wyłącz blokowanie wyskakujących wyskakujących w przeglądarce dla `studio.azureml.net`. Jeśli korzystasz z przeglądarki Google Chrome, pobierz i zainstaluj jedną z kilku wtyczek dostępnych w Google Chrome WebStore [Kliknij raz rozszerzenie aplikacji](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Środowisko uruchomieniowe integracji usługi Azure Data Factory było wcześniej znane jako brama zarządzania danymi. Samouczek krok po kroku będzie nadal odnosić się do niego jako bramy.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Tworzenie bramy
Pierwszym krokiem jest utworzenie i skonfigurowanie bramy w celu uzyskania dostępu do lokalnej bazy danych SQL.

1. Zaloguj się do [usługi Azure Machine Learning Studio (klasyczna)](https://studio.azureml.net/Home/) i wybierz obszar roboczy, w który chcesz pracować.
2. Kliknij blok **USTAWIENIA** po lewej stronie, a następnie kliknij kartę **BRAMY DANYCH** u góry.
3. Kliknij **pozycję NOWA BRAMA DANYCH** u dołu ekranu.

    ![Nowa brama danych](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. W oknie dialogowym **Nowa brama danych** wprowadź **nazwę bramy** i opcjonalnie dodaj **opis**. Kliknij strzałkę w prawym dolnym rogu, aby przejść do następnego kroku konfiguracji.

    ![Wprowadź nazwę i opis bramy](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. W oknie dialogowym Pobieranie i rejestrowanie bramy danych skopiuj klucz rejestracji bramy do schowka.

    ![Pobieranie i rejestrowanie bramy danych](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Jeśli brama zarządzania danymi firmy Microsoft nie została jeszcze pobrana i zainstalowana, kliknij pozycję **Pobierz bramę zarządzania danymi**. Spowoduje to przejście do Centrum pobierania Microsoft, gdzie można wybrać potrzebną wersję bramy, pobrać ją i zainstalować. Szczegółowe informacje na temat wymagań wstępnych dotyczących instalacji, kroków instalacji i wskazówek dotyczących rozwiązywania problemów można znaleźć w początkowych sekcjach artykułu [Przenoszenie danych między źródłami lokalnymi a chmurą za pomocą bramy zarządzania danymi](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po zainstalowaniu bramy zostanie otwarty menedżer konfiguracji bramy zarządzania danymi i zostanie wyświetlone okno dialogowe **Zarejestruj bramę.** Wklej **klucz rejestracji bramy** skopiowany do schowka i kliknij przycisk **Zarejestruj**.
8. Jeśli masz już zainstalowaną bramę, uruchom program Menedżer konfiguracji bramy zarządzania danymi. Kliknij **przycisk Zmień klucz**, wklej klucz rejestracji **bramy** skopiowany do schowka w poprzednim kroku, a następnie kliknij przycisk **OK**.
9. Po zakończeniu instalacji zostanie wyświetlone okno dialogowe **Rejestrowanie bramy** dla programu Microsoft Data Management Gateway Configuration Manager. Wklej klucz rejestracji bramy skopiowany do schowka w poprzednim kroku i kliknij przycisk **Zarejestruj**.

    ![Rejestrowanie bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfiguracja bramy jest zakończona po ustawieniu następujących wartości na karcie **Narzędzia główne** w programie Microsoft Data Management Gateway Configuration Manager:

    * **Nazwa bramy** i **nazwa wystąpienia** są ustawione na nazwę bramy.
    * **Rejestracja** jest ustawiona na **Zarejestrowane**.
    * **Stan** jest ustawiony na **Rozpoczęty**.
    * Na pasku stanu u dołu zostanie wyświetlony komunikat **Połączony z usługą w chmurze bramy zarządzania danymi** wraz z zielonym znacznikiem wyboru.

      ![Menedżer bramy zarządzania danymi](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Usługa Azure Machine Learning Studio (klasyczna) również jest aktualizowana po pomyślnym zakończeniu rejestracji.

    ![Rejestracja bramy powiodła się](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. W oknie dialogowym **Pobieranie i rejestrowanie bramy danych** kliknij znacznik wyboru, aby zakończyć konfigurację. Na stronie **Ustawienia** jest wyświetlany stan bramy jako "Online". W okienku po prawej stronie znajdziesz status i inne przydatne informacje.

    ![Ustawienia bramy](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. W programie Microsoft Data Management Gateway Menedżer konfiguracji przełącznik do **certyfikatu** kartę. Certyfikat określony na tej karcie jest używany do szyfrowania/odszyfrowywania poświadczeń dla lokalnego magazynu danych określonego w portalu. Ten certyfikat jest certyfikatem domyślnym. Firma Microsoft zaleca zmianę tego na własny certyfikat, który jest owy w systemie zarządzania certyfikatami. Kliknij **przycisk Zmień,** aby zamiast tego użyć własnego certyfikatu.

    ![Zmienianie certyfikatu bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcjonalnie) Jeśli chcesz włączyć pełne rejestrowanie w celu rozwiązywania problemów z bramą, w programie Microsoft Data Management Gateway Configuration Manager przełącz się do karty **Diagnostyka** i sprawdź opcję **Włącz rejestrowanie pełne do rozwiązywania problemów.** Informacje dotyczące rejestrowania można znaleźć w Podglądzie zdarzeń systemu Windows w węźle - &gt; **Brama zarządzania danymi** **dziennikami aplikacji i usług.** Można również użyć **diagnostyki** kartę, aby przetestować połączenie z lokalnym źródłem danych przy użyciu bramy.

    ![Włącz pełne rejestrowanie](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Spowoduje to ukończenie procesu konfiguracji bramy w usłudze Azure Machine Learning Studio (klasyczny).
Teraz możesz używać danych lokalnych.

Można utworzyć i skonfigurować wiele bram w Studio (klasyczny) dla każdego obszaru roboczego. Na przykład może mieć bramę, która ma połączyć się ze źródłami danych testowych podczas programisty i inną bramę dla źródeł danych produkcyjnych. Usługa Azure Machine Learning Studio (klasyczna) zapewnia elastyczność konfigurowania wielu bram w zależności od środowiska firmowego. Obecnie nie można współużytkować bramy między obszarami roboczymi i tylko jedna brama może być zainstalowana na jednym komputerze. Aby uzyskać więcej informacji, zobacz [Przenoszenie danych między źródłami lokalnymi a chmurą za pomocą bramy zarządzania danymi](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Użyj bramy do odczytu danych z lokalnego źródła danych
Po skonfigurowaniu bramy można dodać moduł **Importuj dane** do eksperymentu, który wprowadza dane z lokalnej bazy danych programu SQL Server.

1. W programie Machine Learning Studio (klasycznym) wybierz kartę **EKSPERYMENTY,** kliknij **pozycję +NOWOŚĆ** w lewym dolnym rogu i wybierz **pozycję Pusty eksperyment** (lub wybierz jeden z kilku dostępnych przykładowych eksperymentów).
2. Znajdź i przeciągnij moduł **Importuj dane** na kanwę eksperymentu.
3. Kliknij **pozycję Zapisz jak** pod kanwą. Wprowadź "Azure Machine Learning Studio (classic) In-Premises SQL Server Tutorial" dla nazwy eksperymentu, wybierz obszar roboczy i kliknij znacznik wyboru **OK.**

   ![Zapisywanie eksperymentu z nową nazwą](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknij moduł **Importuj dane,** aby go **zaznaczyć,** a następnie w okienku Właściwości po prawej stronie kanwy wybierz pozycję "Lokalna baza danych SQL" na liście rozwijanej **Źródło danych.**
5. Wybierz instalowana i **zarejestrowana brama danych.** Inną bramę można skonfigurować, wybierając opcję "(dodaj nową bramę danych...)".

   ![Wybierz bramę danych dla modułu Importuj dane](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Wprowadź **nazwę serwera bazy danych** SQL i nazwę bazy **danych**wraz z **kwerendą bazy danych** SQL, którą chcesz wykonać.
7. Kliknij **pozycję Wprowadź wartości** w obszarze Nazwa użytkownika i **hasło** i wprowadź poświadczenia bazy danych. Uwierzytelniania zintegrowanego systemu Windows lub uwierzytelniania programu SQL Server można używać w zależności od konfiguracji lokalnego programu SQL Server.

   ![Wprowadzanie poświadczeń bazy danych](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Komunikat "wartości wymagane" zmienia się na "wartości ustawione" z zielonym znacznikiem wyboru. Poświadczenia należy wprowadzić tylko raz, chyba że zmienią się informacje o bazie danych lub hasło. Usługa Azure Machine Learning Studio (klasyczna) używa certyfikatu dostarczonego podczas instalowania bramy do szyfrowania poświadczeń w chmurze. Platforma Azure nigdy nie przechowuje poświadczeń lokalnych bez szyfrowania.

   ![Importowanie właściwości modułu danych](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknij **przycisk URUCHOM,** aby uruchomić eksperyment.

Po zakończeniu eksperymentu można wizualizować dane zaimportowane z bazy danych, klikając port wyjściowy modułu **Importuj dane** i wybierając opcję **Wizualizuj**.

Po zakończeniu tworzenia eksperymentu można wdrożyć i operacjonalizacji modelu. Korzystając z usługi wykonywania wsadowego, dane z lokalnej bazy danych programu SQL Server skonfigurowane w module **Importuj dane** będą odczytywane i używane do oceniania. Chociaż można użyć usługi odpowiedzi na żądania do oceniania danych lokalnych, firma Microsoft zaleca użycie [dodatku programu Excel.](excel-add-in-for-web-services.md) Obecnie zapisywanie do lokalnej bazy danych programu SQL Server za pośrednictwem **danych eksportu** nie jest obsługiwane ani w eksperymentach, ani w opublikowanych usługach sieci web.
