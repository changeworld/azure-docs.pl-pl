---
title: Na lokalnym serwerze SQL Server
titleSuffix: Azure Machine Learning Studio
description: Użyj danych z lokalnej bazy danych programu SQL Server, przeprowadzanie zaawansowanych analiz za pomocą usługi Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9590728cec663b36c889dc26a6216c3d474244e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735467"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-using-an-on-premises-sql-server-database"></a>Przeprowadzanie analiz przy użyciu usługi Azure Machine Learning Studio korzystania z bazy danych programu SQL Server w środowisku lokalnym

Często w przypadku przedsiębiorstw, które działają z danymi lokalnymi chce wykorzystują skali i elastyczności chmury w celu ich usługi machine learning obciążeń. Ale nie chcesz przerwać bieżącego procesów biznesowych i przepływów pracy przez przeniesienie ich danych lokalnych do chmury. Usługa Azure Machine Learning Studio obsługuje teraz odczytywanie danych z bazy danych programu SQL Server w środowisku lokalnym i następnie szkolenia i oceniania modelu przy użyciu tych danych. Nie masz już ręcznie skopiować i synchronizowanie danych między chmurą a serwerem w środowisku lokalnym. Zamiast tego **importu danych** modułu w usłudze Azure Machine Learning Studio może teraz odczytywać bezpośrednio z lokalną bazą danych programu SQL Server do szkolenia i oceniania zadania.

Ten artykuł zawiera omówienie sposobu transferu danych przychodzących lokalnych danych programu SQL server do usługi Azure Machine Learning Studio. Przyjęto założenie, że znasz Studio koncepcje, takie jak obszary robocze, moduły, zestawy danych, eksperymenty *itp.*.

> [!NOTE]
> Ta funkcja nie jest dostępna dla bezpłatnych obszarów roboczych. Aby uzyskać więcej informacji na temat cen usługi Machine Learning i warstwy, zobacz [Azure Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Zainstaluj Self-Hosted Integration Runtime usługi Data Factory
Aby uzyskać dostęp do bazy danych lokalnego programu SQL Server w usłudze Azure Machine Learning Studio, musisz pobrać i zainstalować z produktem Integration Runtime usługi Data Factory, znana wcześniej jako brama zarządzania danymi. Po skonfigurowaniu połączenia w usłudze Machine Learning Studio, masz możliwość pobrania i zainstalowania Integration Runtime (IR), za pomocą **pobierania i bramy danych rejestru** okna dialogowego opisanego poniżej.


Można też zainstalować środowisko IR wcześniej, pobierając i uruchamiając pakiet Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Plik MSI może służyć także do uaktualnienia istniejącego środowiska IR do najnowszej wersji przy użyciu wszystkich ustawień zachowane.

Data Factory środowiskiem Integration Runtime ma następujące wymagania wstępne:

* Integracja własne fabryki danych wymaga 64-bitowego systemu operacyjnego za pomocą programu .NET Framework 4.6.1 lub nowszej.
* Obsługiwane wersje systemu operacyjnego Windows są systemu Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Zalecana konfiguracja maszyny IR jest co najmniej 2 GHz, 4 rdzenie Procesora, 8GB pamięci RAM i 80GB dysku.
* Jeśli komputer hosta przechodzi w stan hibernacji, środowisko IR nie będzie odpowiadać na żądania danych. W związku z tym skonfigurować plan zasilania odpowiednich na komputerze przed zainstalowaniem IR. Jeśli komputer jest skonfigurowany do hibernacji, instalacja środowiska IR wyświetla komunikat.
* Ponieważ działania kopiowania odbywa się na określonej częstotliwości, użycie zasobów (procesor CPU, pamięci) na maszynie również zgodna z szczytowe i czasach bezczynności tego samego wzorca. Wykorzystanie zasobów zależy również od intensywnie ilości danych przenoszonych. W przypadku wielu zadań kopiowania w toku, zauważysz użycia zasobów wzrosną godzinami szczytu. W trakcie z technicznego punktu widzenia wystarczające minimalnej konfiguracji wymienionych powyżej można mieć konfigurację więcej zasobów niż minimalna konfiguracja, w zależności od określonego obciążenia w przypadku przenoszenia danych.

Rozważ następujące opcje podczas konfigurowania i używania danych fabryki własne środowisko IR:

* Można zainstalować tylko jedno wystąpienie środowiska IR na pojedynczym komputerze.
* Za pomocą jednego środowiska IR do wielu źródeł danych w środowisku lokalnym.
* Do tego samego źródła danych w środowisku lokalnym, możesz połączyć wiele środowisk IR na różnych komputerach.
* Możesz skonfigurować IRs dla tylko jednego obszaru roboczego w danym momencie. Obecnie IRs nie można udostępnić w obszarach roboczych.
* Można skonfigurować wiele IRs dla jednego obszaru roboczego. Na przykład można użyć środowiska IR, który jest połączony ze źródłami danych testowych podczas rozwoju i produkcji IR, gdy wszystko będzie gotowe do obsługi operacji.
* Środowisko IR nie musi znajdować się na tym samym komputerze co źródło danych. Jednak dokonywanie aktualizacji na bliżej źródła danych skraca czas dla bramy do połączenia ze źródłem danych. Firma Microsoft zaleca instalowanie środowiska IR na komputerze, który jest inny niż ten, który jest hostem lokalnym źródłem danych, aby bramy i źródła danych nie konkurują o zasoby.
* Jeśli masz już środowisko IR zainstalowana na danym komputerze obsługująca scenariusze usługi Power BI lub usługi Azure Data Factory, należy zainstalować oddzielne środowiska IR Azure Machine Learning Studio na innym komputerze.

  > [!NOTE]
  > Nie można uruchomić produktu Integration Runtime usługi Data Factory i Power BI Gateway, w tym samym komputerze.
  >
  >
* Należy użyć Data Factory własne środowisko IR Azure Machine Learning Studio, nawet wtedy, gdy używasz usługi Azure ExpressRoute dla innych danych. Źródło danych należy traktować jako źródło danych w środowisku lokalnym, (która znajduje się za zaporą) nawet gdy korzystasz z usługi ExpressRoute. Data Factory własne środowisko IR umożliwia nawiązanie łączności między uczenia maszynowego i źródła danych.

Szczegółowe informacje dotyczące wymagań wstępnych instalacji, kroków instalacji i wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule [środowiska Integration Runtime w usłudze Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Danych przychodzących z lokalnej bazy danych programu SQL Server do usługi Azure Machine Learning
W tym przewodniku zostanie Konfigurowanie Integration Runtime usługi Azure Data Factory w obszarze roboczym usługi Azure Machine Learning, skonfigurować go i następnie odczytywać dane z lokalnej bazy danych programu SQL Server.

> [!TIP]
> Przed rozpoczęciem należy wyłączyć blokowanie wyskakujących okienek w przeglądarce dla `studio.azureml.net`. Jeśli używasz przeglądarki Google Chrome, Pobierz i zainstaluj jedną z kilku dostępnych wtyczek w sklepie WebStore programu Google Chrome [kliknij raz rozszerzenia aplikacji](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Usługa Azure Data Factory własne środowisko IR była wcześniej znana jako brama zarządzania danymi. Samouczek krok po kroku będą nadal odwoływać się do niego jako brama.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Tworzenie bramy
Pierwszym krokiem jest do tworzenia i konfigurowania bramy, dostęp do bazy danych SQL w środowisku lokalnym.

1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/Home/) i wybierz obszar roboczy, który chcesz pracować w.
2. Kliknij przycisk **ustawienia** blok po lewej stronie, a następnie kliknij przycisk **bram DATA GATEWAYS** kartę u góry.
3. Kliknij przycisk **nowej bramy DATA GATEWAY** w dolnej części ekranu.

    ![Nowa brama danych](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. W **nowej bramy data gateway** okno dialogowe, wprowadź **nazwę bramy** i opcjonalnie Dodaj **opis**. Kliknij strzałkę w prawym dolnym rogu, aby przejść do następnego kroku konfiguracji.

    ![Wprowadź nazwę bramy i opis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. W oknie Pobieranie i rejestrowanie danych bramy dialogowym klucz rejestracji bramy należy skopiować do Schowka.

    ![Pobierz i zarejestruj bramę danych](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Jeśli jeszcze nie zostały pobrane i zainstalowane brama zarządzania danymi firmy Microsoft, kliknij przycisk **bramy zarządzania danymi pobierania**. Spowoduje to przejście do witryny Microsoft Download Center, gdzie można wybrać wersję bramy, czego potrzebujesz, pobierz go i zainstaluj go. Szczegółowe informacje dotyczące wymagań wstępnych instalacji, kroków instalacji i wskazówki dotyczące rozwiązywania problemów można znaleźć w sekcjach początku tego artykułu [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzania danymi](../../data-factory/tutorial-hybrid-copy-portal.md) .
7. Po zainstalowaniu bramy spowoduje otwarcie Menedżera konfiguracji bramy zarządzania danych i **bramy rejestru** zostanie wyświetlone okno dialogowe. Wklej **klucz rejestracji bramy** skopiowane do Schowka i kliknij przycisk **zarejestrować**.
8. Jeśli masz już zainstalowanej bramy, uruchom Menedżera konfiguracji bramy zarządzania danych. Kliknij przycisk **Zmień klucz**, Wklej **klucz rejestracji bramy** skopiowano do Schowka w poprzednim kroku i kliknij **OK**.
9. Po zakończeniu instalacji, **bramy rejestru** zostanie wyświetlone okno dialogowe dla firmy Microsoft danych Menedżera konfiguracji bramy zarządzania. Wklej klucz rejestracji bramy, który został skopiowany do Schowka w poprzednim kroku, a następnie kliknij przycisk **zarejestrować**.

    ![Rejestrowanie bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfiguracja bramy jest ukończona, gdy następujące wartości są ustawione na **Home** kartę w Microsoft danych Menedżera konfiguracji bramy zarządzania:

    * **Nazwa bramy** i **nazwa wystąpienia** są ustawione na nazwę bramy.
    * **Rejestracja** ustawiono **zarejestrowanej**.
    * **Stan** ustawiono **uruchomiono**.
    * Pasek stanu u Wyświetla dolnej **połączyć usługę Cloud bramy zarządzania danymi** wraz z zielonym znacznikiem wyboru.

      ![Menedżer bramy zarządzania danych](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Usługa Azure Machine Learning Studio również jest aktualizowany po pomyślnej rejestracji.

    ![Rejestracja bramy zakończona powodzeniem](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. W **Pobierz i zarejestruj bramę danych** okno dialogowe, kliknij znacznik wyboru, aby ukończyć instalację. **Ustawienia** strony wyświetli stan bramy jako "Online". W okienku po prawej stronie znajdziesz, stan i inne przydatne informacje.

    ![Ustawienia bramy](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. W programie Microsoft danych zarządzania Gateway Configuration Manager przejdź do **certyfikatu** kartę. Certyfikat określony na tej karcie jest używana do szyfrowania/odszyfrowywania poświadczeń dla magazynu danych w środowisku lokalnym, który określisz w portalu. Ten certyfikat jest certyfikatem domyślne. Firma Microsoft zaleca zmianę to na tworzenie kopii zapasowej w systemie zarządzania certyfikatów własny certyfikat. Kliknij przycisk **zmiany** zamiast tego użyć własnego certyfikatu.

    ![Certyfikat bramy zmiany](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcjonalnie) Jeśli chcesz włączyć pełne rejestrowanie w celu rozwiązania zgłoszonego problemach dotyczących bramy w programie Microsoft Data Menedżera konfiguracji bramy zarządzania Przełącz się do **diagnostyki** i sprawdź **włączyć pełne rejestrowanie na potrzeby rozwiązywania problemów** opcji. Rejestrowanie informacji można znaleźć w Podglądzie zdarzeń Windows w obszarze **Dzienniki aplikacji i usług**  - &gt; **bramy zarządzania danymi** węzła. Można również użyć **diagnostyki** kartę, aby przetestować połączenie z lokalnym źródłem danych za pomocą bramy.

    ![Włącz pełne rejestrowanie](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Na tym kończy proces instalacji bramy w usłudze Azure Machine Learning Studio.
Teraz możesz użyć danych w środowisku lokalnym.

Można utworzyć i skonfigurować wiele bram w programie Studio dla każdego obszaru roboczego. Na przykład masz bramy, który chcesz połączyć ze źródłami danych testowych podczas tworzenia i inną bramę dla źródła danych produkcyjnych. Usługa Azure Machine Learning Studio zapewnia elastyczność, aby skonfigurować wiele bram zależności w środowisku firmowym. Obecnie nie można udostępniać bramy między obszarami roboczymi i na jednym komputerze można zainstalować tylko jedną bramę. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzania danymi](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Odczytywanie danych z lokalnego źródła danych za pomocą bramy
Po skonfigurowaniu bramy można dodać **importu danych** modułów na eksperyment, który danych wejściowych w dane z lokalnej bazy danych programu SQL Server.

1. W usłudze Machine Learning Studio, wybierz **EKSPERYMENTÓW** kliknij pozycję **+ nowy** w lewym dolnym rogu i wybierz **pusty eksperyment** (lub wybierz jedną z kilku przykładowych eksperymenty dostępne).
2. Znajdowanie i przeciąganie **importu danych** modułu do obszaru roboczego eksperymentu.
3. Kliknij przycisk **Zapisz jako** poniżej obszaru roboczego. Wprowadź "Azure Studio On-Premises SQL Server samouczek dotyczący uczenia maszynowego" Nazwa eksperymentu, wybierz obszar roboczy, a następnie kliknij przycisk **OK** znacznik wyboru.

   ![Zapisz eksperymentu z nową nazwą](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknij przycisk **importu danych** modułu, aby ją zaznaczyć, następnie w **właściwości** w okienku po prawej stronie kanwy, wybierz opcję "On-Premises SQL Database" w **źródła danych** listy rozwijanej.
5. Wybierz **bramy Data gateway** zainstalowany i zarejestrowany. Możesz skonfigurować inną bramę, wybierając pozycję "(Dodaj nową bramę danych...)".

   ![Wybierz bramy danych dla modułu importu danych](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Wprowadź SQL **nazwy serwera bazy danych** i **Nazwa bazy danych**, wraz z SQL **zapytanie bazy danych** chcesz wykonać.
7. Kliknij przycisk **wprowadź wartości** w obszarze **nazwy użytkownika i hasła** i wprowadź swoje poświadczenia bazy danych. Można użyć zintegrowanego uwierzytelniania Windows lub uwierzytelniania programu SQL Server, w zależności od sposobu skonfigurowania na lokalnym serwerze SQL Server.

   ![Wprowadź poświadczenia bazy danych](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Komunikat "wymagane wartości" zmiany "zestaw wartości" z zielonym znacznikiem wyboru. Wystarczy raz wprowadź poświadczenia, chyba że zmiany informacji o bazie danych lub hasła. Usługa Azure Machine Learning Studio, używa certyfikatu, podane podczas instalacji bramy do szyfrowania poświadczeń w chmurze. Azure nigdy nie przechowuje poświadczeń lokalnych bez szyfrowania.

   ![Importowanie danych właściwości modułu](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknij przycisk **Uruchom** do uruchamiania eksperymentu.

Po zakończeniu eksperymentu można wizualizować dane zaimportowane z bazy danych, klikając pozycję port wyjściowy **importu danych** modułu i wybierając polecenie **Visualize**.

Po zakończeniu tworzenia eksperymentu można wdrożyć i operacjonalizować model. Za pomocą usługi wykonywania wsadowego, dane z lokalnego serwera SQL bazy danych skonfigurowanych w **importu danych** moduł będzie odczytywać i używane do oceniania. Podczas oceniania lokalnych danych, można użyć usługę odpowiedzi na żądanie, firma Microsoft zaleca używanie [dodatek programu Excel](excel-add-in-for-web-services.md) zamiast tego. Obecnie usługa zapisywania do bazy danych programu SQL Server w środowisku lokalnym za pośrednictwem **Eksport danych** nie jest obsługiwane albo w eksperymenty lub opublikowane w sieci web usług.
