---
title: Na lokalnym serwerze SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Użyj danych z lokalnej bazy danych SQL Server, aby przeprowadzić zaawansowaną analizę przy użyciu Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9afac1adef801956f176dd339c795e2df533a2c7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169116"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Wykonywanie analizy z Azure Machine Learning Studio (klasyczny) przy użyciu lokalnej bazy danych SQL Server

Często w przypadku przedsiębiorstw, które działają z danymi lokalnymi chce wykorzystują skali i elastyczności chmury w celu ich usługi machine learning obciążeń. Ale nie chcesz przerwać bieżącego procesów biznesowych i przepływów pracy przez przeniesienie ich danych lokalnych do chmury. Azure Machine Learning Studio (klasyczny) obsługuje teraz odczytywanie danych z lokalnej SQL Server bazy danych, a następnie uczenie i ocenianie modelu przy użyciu tych danych. Nie masz już ręcznie skopiować i synchronizowanie danych między chmurą a serwerem w środowisku lokalnym. Zamiast tego moduł **Importuj dane** w Azure Machine Learning Studio (klasyczny) można teraz odczytywać bezpośrednio z lokalnej bazy danych SQL Server do celów szkoleniowych i oceniających.

Ten artykuł zawiera omówienie sposobu transferowania lokalnych danych programu SQL Server do Azure Machine Learning Studio (klasyczne). Przyjęto założenie, że znane są koncepcje programu Studio (klasyczne), takie jak obszary robocze, moduły, zestawy danych, eksperymenty *itp.*

> [!NOTE]
> Ta funkcja nie jest dostępna dla bezpłatnych obszarów roboczych. Aby uzyskać więcej informacji o Machine Learning cenach i warstwach, zobacz [Azure Machine Learning cennika](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Zainstaluj Self-Hosted Integration Runtime usługi Data Factory
Aby uzyskać dostęp do lokalnej bazy danych SQL Server w Azure Machine Learning Studio (klasyczny), należy pobrać i zainstalować Data Factory samodzielnej Integration Runtime, wcześniej znanej jako brama Zarządzanie danymi. Po skonfigurowaniu połączenia w Machine Learning Studio (klasyczny) można pobrać i zainstalować Integration Runtime (IR) przy użyciu okna dialogowego **Pobierz i zarejestruj bramę danych** opisaną poniżej.


Możesz również zainstalować środowisko IR wcześniej, pobierając i uruchamiając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Plik MSI może również służyć do uaktualniania istniejącego środowiska IR do najnowszej wersji z zachowaniem wszystkich ustawień.

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
* Urząd Skarbowy można skonfigurować tylko dla jednego obszaru roboczego. Obecnie IRs nie można udostępnić w obszarach roboczych.
* Można skonfigurować wiele IRs dla jednego obszaru roboczego. Na przykład możesz chcieć użyć podczerwieni połączonej ze źródłami danych testowych podczas opracowywania i produkcyjnego środowiska IR, gdy wszystko jest gotowe do operacjonalizować.
* Środowisko IR nie musi znajdować się na tym samym komputerze co źródło danych. Jednak dokonywanie aktualizacji na bliżej źródła danych skraca czas dla bramy do połączenia ze źródłem danych. Firma Microsoft zaleca instalowanie środowiska IR na komputerze, który jest inny niż ten, który jest hostem lokalnym źródłem danych, aby bramy i źródła danych nie konkurują o zasoby.
* Jeśli na komputerze zainstalowano już środowisko IR obsługujące Power BI lub Azure Data Factory scenariusze, zainstaluj oddzielne środowisko IR dla Azure Machine Learning Studio (klasycznego) na innym komputerze.

  > [!NOTE]
  > Nie można uruchomić produktu Integration Runtime usługi Data Factory i Power BI Gateway, w tym samym komputerze.
  >
  >
* Musisz użyć Data Factory samodzielnego Integration Runtime do Azure Machine Learning Studio (klasyczny), nawet jeśli używasz platformy Azure ExpressRoute do innych danych. Źródło danych należy traktować jako źródło danych w środowisku lokalnym, (która znajduje się za zaporą) nawet gdy korzystasz z usługi ExpressRoute. Data Factory własne środowisko IR umożliwia nawiązanie łączności między uczenia maszynowego i źródła danych.

Szczegółowe informacje na temat wymagań wstępnych instalacji, kroki instalacji i wskazówki dotyczące rozwiązywania problemów znajdują się w artykule [Integration Runtime w Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dane przychodzące z lokalnej bazy danych SQL Server do Azure Machine Learning
W tym instruktażu skonfigurujesz Integration Runtime Azure Data Factory w obszarze roboczym Azure Machine Learning, skonfigurujesz ją, a następnie odczytasz dane z lokalnej bazy danych SQL Server.

> [!TIP]
> Przed rozpoczęciem należy wyłączyć blokowanie wyskakujących okienek w przeglądarce dla `studio.azureml.net`. Jeśli używasz przeglądarki Google Chrome, Pobierz i Zainstaluj jedną z kilku wtyczek dostępnych w sklepie Google Chrome webstore [kliknij pozycję po rozszerzeniu aplikacji](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Usługa Azure Data Factory własne środowisko IR była wcześniej znana jako brama zarządzania danymi. Samouczek krok po kroku będą nadal odwoływać się do niego jako brama.  

### <a name="step-1-create-a-gateway"></a>Krok 1: Tworzenie bramy
Pierwszym krokiem jest do tworzenia i konfigurowania bramy, dostęp do bazy danych SQL w środowisku lokalnym.

1. Zaloguj się do [Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net/Home/) i wybierz obszar roboczy, w którym chcesz korzystać.
2. Kliknij blok **Ustawienia** po lewej stronie, a następnie kliknij kartę **bramy danych** w górnej części ekranu.
3. Kliknij pozycję **Nowa brama danych** w dolnej części ekranu.

    ![Nowa brama danych](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. W oknie dialogowym **Nowa brama danych** wprowadź **nazwę bramy** i opcjonalnie Dodaj **Opis**. Kliknij strzałkę w prawym dolnym rogu, aby przejść do następnego kroku konfiguracji.

    ![Wprowadź nazwę bramy i opis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. W oknie Pobieranie i rejestrowanie danych bramy dialogowym klucz rejestracji bramy należy skopiować do Schowka.

    ![Pobierz i zarejestruj bramę danych](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Jeśli nie pobrano jeszcze i nie zainstalowano bramy Zarządzanie danymi firmy Microsoft, kliknij pozycję **Pobierz bramę zarządzania danymi**. Spowoduje to przejście do witryny Microsoft Download Center, gdzie można wybrać wersję bramy, czego potrzebujesz, pobierz go i zainstaluj go. Aby uzyskać szczegółowe informacje na temat wymagań wstępnych instalacji, czynności instalacyjnych i wskazówek dotyczących rozwiązywania problemów, należy [przenieść dane między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po zainstalowaniu bramy zostanie otwarta Zarządzanie danymi Configuration Manager Gateway i zostanie wyświetlone okno dialogowe **Rejestrowanie bramy** . Wklej **klucz rejestracji bramy** skopiowany do schowka, a następnie kliknij pozycję **zarejestruj**.
8. Jeśli masz już zainstalowanej bramy, uruchom Menedżera konfiguracji bramy zarządzania danych. Kliknij przycisk **Zmień klucz**, wklej **klucz rejestracji bramy** skopiowany do Schowka w poprzednim kroku, a następnie kliknij przycisk **OK**.
9. Po zakończeniu instalacji zostanie wyświetlone okno dialogowe **Rejestrowanie bramy** dla programu Microsoft Zarządzanie danymi Gateway Configuration Manager. Wklej klucz rejestracji bramy skopiowany do Schowka w poprzednim kroku, a następnie kliknij pozycję **zarejestruj**.

    ![Rejestrowanie bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfiguracja bramy została ukończona, gdy na karcie **Narzędzia główne** w usłudze Microsoft Zarządzanie danymi Gateway Configuration Manager zostanie ustawiona następująca wartość:

    * Nazwa **bramy** i **Nazwa wystąpienia** są ustawione na nazwę bramy.
    * **Rejestracja** jest ustawiona na **zarejestrowane**.
    * **Stan** jest ustawiony na wartość **uruchomiony**.
    * Na dolnej stronie pojawi się pasek stanu **połączony z usługą Zarządzanie danymi Gateway w chmurze** oraz zielony znacznik wyboru.

      ![Menedżer bramy zarządzania danych](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klasyczny) jest również aktualizowany po pomyślnym zakończeniu rejestracji.

    ![Rejestracja bramy zakończona powodzeniem](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. W oknie dialogowym **Pobierz i zarejestruj bramę danych** kliknij znacznik wyboru, aby zakończyć instalację. Na stronie **Ustawienia** zostanie wyświetlony stan bramy "online". W okienku po prawej stronie znajdziesz, stan i inne przydatne informacje.

    ![Ustawienia bramy](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. W Configuration Manager Microsoft Zarządzanie danymi Gateway przejdź do karty **certyfikat** . Certyfikat określony na tej karcie jest używany do szyfrowania/odszyfrowywania poświadczeń dla lokalnego magazynu danych określonego w portalu. Ten certyfikat jest certyfikatem domyślne. Firma Microsoft zaleca zmianę to na tworzenie kopii zapasowej w systemie zarządzania certyfikatów własny certyfikat. Kliknij przycisk **Zmień** , aby zamiast tego użyć własnego certyfikatu.

    ![Certyfikat bramy zmiany](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. obowiązkowe Jeśli chcesz włączyć pełne rejestrowanie w celu rozwiązywania problemów z bramą, w Configuration Manager Microsoft Zarządzanie danymi Gateway przejdź do karty **Diagnostyka** i zaznacz opcję **Włącz pełne rejestrowanie w celu rozwiązywania problemów** . Informacje dotyczące rejestrowania znajdują się w Podgląd zdarzeń systemu Windows w obszarze **Dzienniki aplikacji i usług** -&gt; **Zarządzanie danymi bramy** . Możesz również użyć karty **Diagnostyka** , aby przetestować połączenie z lokalnym źródłem danych przy użyciu bramy.

    ![Włącz pełne rejestrowanie](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Spowoduje to zakończenie procesu instalacji bramy w Azure Machine Learning Studio (klasyczny).
Teraz możesz użyć danych w środowisku lokalnym.

Można utworzyć i skonfigurować wiele bram w programie Studio (klasyczny) dla każdego obszaru roboczego. Na przykład masz bramy, który chcesz połączyć ze źródłami danych testowych podczas tworzenia i inną bramę dla źródła danych produkcyjnych. Azure Machine Learning Studio (klasyczny) zapewnia elastyczność konfigurowania wielu bram w zależności od środowiska firmowego. Obecnie nie można udostępniać bramy między obszarami roboczymi i na jednym komputerze można zainstalować tylko jedną bramę. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Używania bramy można odczytać danych z lokalnego źródła danych
Po skonfigurowaniu bramy można dodać do eksperymentu moduł **danych importu** , który wprowadza dane z lokalnej bazy danych SQL Server.

1. W Machine Learning Studio (klasyczny) wybierz kartę **eksperymenty** , kliknij pozycję **+ Nowy** w lewym dolnym rogu, a następnie wybierz pozycję **pusty eksperyment** (lub wybierz jedną z kilku dostępnych przykładowych eksperymentów).
2. Znajdź i przeciągnij moduł **Import danych** do kanwy eksperymentu.
3. Kliknij przycisk **Zapisz jako** poniżej kanwy. W polu Nazwa eksperymentu wpisz "Azure Machine Learning Studio (klasyczny) SQL Server, wybierz obszar roboczy, a następnie kliknij znacznik wyboru **OK** .

   ![Zapisz eksperymentu z nową nazwą](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknij moduł **Importuj dane** , aby go zaznaczyć, a następnie w okienku **Właściwości** z prawej strony kanwy wybierz pozycję "SQL Database lokalne" na liście rozwijanej **źródła danych** .
5. Wybierz zainstalowaną i zarejestrowaną **bramę danych** . Możesz skonfigurować inną bramę, wybierając pozycję "(Dodaj nową bramę danych...)".

   ![Wybierz bramy danych dla modułu importu danych](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Wprowadź **nazwę serwera bazy danych** SQL i **nazwę bazy danych**wraz z **kwerendą bazy danych** SQL, która ma zostać wykonana.
7. Kliknij pozycję **wprowadź wartości** w obszarze **Nazwa użytkownika i hasło** , a następnie wprowadź swoje poświadczenia bazy danych. Można użyć zintegrowanego uwierzytelniania Windows lub uwierzytelniania programu SQL Server, w zależności od sposobu skonfigurowania na lokalnym serwerze SQL Server.

   ![Wprowadź poświadczenia bazy danych](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Komunikat "wymagane wartości" zmiany "zestaw wartości" z zielonym znacznikiem wyboru. Wystarczy raz wprowadź poświadczenia, chyba że zmiany informacji o bazie danych lub hasła. Azure Machine Learning Studio (klasyczny) używa certyfikatu podanego podczas instalacji bramy w celu szyfrowania poświadczeń w chmurze. Azure nigdy nie przechowuje poświadczeń lokalnych bez szyfrowania.

   ![Importowanie danych właściwości modułu](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknij przycisk **Uruchom** , aby uruchomić eksperyment.

Po zakończeniu eksperymentu można wizualizować dane zaimportowane z bazy danych, klikając port wyjściowy modułu **Importuj dane** i wybierając opcję **Wizualizuj**.

Po zakończeniu tworzenia eksperymentu można wdrożyć i operacjonalizować model. Przy użyciu usługi Batch Execution dane z lokalnej SQL Serverej bazy danych skonfigurowanej w module **Importuj dane** zostaną odczytane i wykorzystane do oceny. Mimo że można użyć usługi odpowiedzi na żądanie do oceniania danych lokalnych, firma Microsoft zaleca użycie [dodatku programu Excel](excel-add-in-for-web-services.md) . Obecnie zapisywanie w lokalnej bazie danych SQL Server za pomocą **eksportu danych** nie jest obsługiwane w doświadczeniach ani opublikowanych usługach sieci Web.
