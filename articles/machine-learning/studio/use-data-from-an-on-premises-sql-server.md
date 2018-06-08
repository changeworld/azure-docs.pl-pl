---
title: Użyj lokalnego serwera SQL w Azure Machine Learning | Dokumentacja firmy Microsoft
description: Za pomocą danych z lokalną bazą danych programu SQL Server można wykonywać zaawansowane analizy w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.openlocfilehash: c3402c824b70d357b68e71ed7cb5783a7489b2b0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837382"
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Wykonywanie zaawansowanych analiz za pomocą usługi Azure Machine Learning, używając danych z lokalnej bazy danych programu SQL Server

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Często w przypadku przedsiębiorstw, które współpracują z lokalnymi danymi chce wykorzystują skali i elastyczność w chmurze na maszynach uczenia obciążeń. Ale nie chcesz przerwać bieżącego procesów biznesowych i przepływów pracy przez przeniesienie ich danych lokalnych do chmury. Usługa Azure Machine Learning obsługuje teraz odczytywanie danych z lokalną bazą danych programu SQL Server, a następnie celów szkoleniowych i oceniania modelu przy użyciu tych danych. Nie należy ręcznie skopiować i synchronizacji danych między chmurą a serwerem lokalnym. Zamiast tego **i zaimportuj dane** modułu w usłudze Azure Machine Learning Studio umożliwia teraz odczyt bezpośrednio z lokalną bazą danych programu SQL Server do szkolenia i oceniania zadania.

Ten artykuł zawiera omówienie sposobu wejściowych lokalnych danych programu SQL server do usługi Azure Machine Learning. Przyjęto założenie, że znasz pojęcia dotyczące usługi Azure Machine Learning obszarów roboczych, modułów, zestawy danych, eksperymentów, *itp.*.

> [!NOTE]
> Ta funkcja nie jest dostępnych bezpłatnych obszarów roboczych. Aby uzyskać więcej informacji o cenach uczenia maszynowego i warstw, zobacz [Azure Machine Learning — cennik](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>Instalowanie bramy zarządzania danymi firmy Microsoft
Aby uzyskać dostęp z lokalną bazą danych programu SQL Server w usłudze Azure Machine Learning, musisz pobrać i zainstalować bramę zarządzania danymi firmy Microsoft. Po skonfigurowaniu połączenia bramy w usłudze Machine Learning Studio, możesz mieć możliwość Pobierz i zainstaluj bramę, używając **Pobierz i zarejestruj bramę danych** okna dialogowego opisanego poniżej.

Można też zainstalować bramę zarządzania danymi wcześniejsze, pobieranie i uruchamiając pakiet Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).
Wybierz najnowszą wersję, wybierając 32-bitowy lub 64-bitowych odpowiednie dla danego komputera. Plik MSI można również uaktualnić istniejącą bramę zarządzania danymi do najnowszej wersji z ustawieniami wszystkie zachowane.

Brama ma następujące wymagania wstępne:

* Obsługiwane wersje systemu operacyjnego systemu Windows to Windows 7, Windows 8/8.1, Windows 10, systemu Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Zalecana konfiguracja maszyna bramy jest co najmniej 2 GHz 4 rdzenie, 8GB pamięci RAM i dysk 80GB.
* Jeśli komputer hosta przejdzie w stan hibernacji, brama nie będzie odpowiadać na żądania danych. W związku z tym Konfigurowanie planu odpowiednie uprawnienia na komputerze przed zainstalowaniem bramy. Jeśli komputer jest skonfigurowany do hibernacji, instalacja bramy wyświetla komunikat.
* Ponieważ działanie kopiowania odbywa się na określonej częstotliwości, użycie zasobów (procesora CPU, pamięci) na maszynie również zgodny ze wzorcem tego samego szczytu i czas bezczynności. Wykorzystanie zasobów zależy również od silnie ilości danych jest przenoszony. W przypadku wielu kopii zadania w toku, widoczny użycia zasobów, do góry w godzinach szczytu. Chociaż technicznie wystarczające minimalnej konfiguracji wymienione powyżej, można w konfiguracji więcej zasobów niż minimalnej konfiguracji, w zależności od określonego obciążenia dla ruchu danych.

Należy rozważyć podczas konfigurowania i korzystania z bramy zarządzania danymi:

* Na jednym komputerze można zainstalować tylko jedno wystąpienie bramy zarządzania danymi.
* Pojedyncza brama służy do lokalnego źródła danych.
* Wiele bram na różnych komputerach można nawiązać to samo źródło danych lokalnych.
* W czasie konfigurowania bramy dla tylko jednego obszaru roboczego. Obecnie Usługa bramy nie można udostępniać między obszarów roboczych.
* Istnieje możliwość skonfigurowania wielu bram dla jednego obszaru roboczego. Na przykład można użyć bramy, która jest połączona z testu źródła danych podczas tworzenia i bramy produkcyjnym po zakończeniu operacjonalizacji.
* Brama nie musi znajdować się na tym samym komputerze co źródła danych. Ale pozostaje zbliżonej do źródła danych przyspiesza bramę, aby połączyć się ze źródłem danych. Zaleca się zainstalować bramę na komputerze, na którym jest inny niż ten, który obsługuje lokalnego źródła danych, dzięki czemu bramy i źródła danych nie konkurują o zasoby.
* Jeśli masz już bramy zainstalowanej na komputerze, obsługująca scenariusze usługi Power BI lub fabryki danych Azure, należy zainstalować oddzielnej bramy dla usługi Azure Machine Learning na innym komputerze.

  > [!NOTE]
  > Nie można uruchomić bramy zarządzania danymi i bramy usługi Power BI na tym samym komputerze.
  >
  >
* Należy użyć bramy zarządzania danymi dla usługi Azure Machine Learning, nawet jeśli używana jest usługa Azure ExpressRoute innych danych. Źródło danych należy traktować jako źródło danych lokalnych (który znajduje się za zaporą) nawet jeśli używasz usługi ExpressRoute. Użyj bramy zarządzania danymi do nawiązywania połączenia między uczenia maszynowego i źródła danych.

Szczegółowe informacje dotyczące wymagań wstępnych instalacji, kroki instalacji i wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule [brama zarządzania danymi](../../data-factory/v1/data-factory-data-management-gateway.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Transfer danych przychodzących danych z lokalnej bazy danych programu SQL Server do usługi Azure Machine Learning
W tym przewodniku będzie skonfigurować bramę zarządzania danymi w obszarze roboczym usługi Azure Machine Learning, jest skonfigurowana, a następnie przeczytaj danych z lokalną bazą danych programu SQL Server.

> [!TIP]
> Przed rozpoczęciem należy wyłączyć blokowanie wyskakujących okienek w przeglądarce dla `studio.azureml.net`. Jeśli używasz przeglądarki Google Chrome, Pobierz i zainstaluj jedną z kilku dostępnych wtyczek w Google Chrome WebStore [kliknij raz rozszerzenia aplikacji](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
>

### <a name="step-1-create-a-gateway"></a>Krok 1: Tworzenie bramy
Pierwszym krokiem jest utworzyć i skonfigurować bramę, dostęp do bazy danych SQL lokalnie.

1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/Home/) i wybierz obszar roboczy, który chcesz pracować.
2. Kliknij przycisk **ustawienia** blok po lewej stronie, a następnie kliknij przycisk **BRAM danych** u góry.
3. Kliknij przycisk **nowej bramy danych** w dolnej części ekranu.

    ![Nowej bramy danych](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. W **nowej bramy danych** okna dialogowego, wprowadź **nazwa bramy** i opcjonalnie Dodaj **opis**. Kliknij strzałkę w prawym dolnym rogu przejść do następnego kroku konfiguracji.

    ![Wprowadź nazwę bramy i opis](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. W przypadku pobierania i rejestrowanie danych bramy okna dialogowego należy skopiować do Schowka klucz rejestracji bramy.

    ![Pobierz i zarejestruj bramę danych](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Jeśli jeszcze nie zostały pobrane i zainstalowane brama zarządzania danymi firmy Microsoft, należy kliknąć **brama zarządzania danymi pobierania**. Powoduje to przejście do programu Microsoft Download Center, której można wybierz wersję bramy, które są potrzebne, pobierz go, a następnie zainstaluj go. Szczegółowe informacje dotyczące wymagań wstępnych instalacji, kroki instalacji i wskazówki dotyczące rozwiązywania problemów można znaleźć w sekcjach początku artykułu [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) .
7. Po zainstalowaniu bramy, Menedżera konfiguracji bramy zarządzania danymi zostanie otwarty i **bramy rejestru** zostanie wyświetlone okno dialogowe. Wklej **klucz rejestracji bramy** został skopiowany do Schowka i kliknij przycisk **zarejestrować**.
8. Jeśli masz już zainstalowana brama, uruchom Menedżera konfiguracji bramy zarządzania danymi. Kliknij przycisk **Zmień klucz**, Wklej **klucz rejestracji bramy** skopiowany do Schowka w poprzednim kroku i kliknij **OK**.
9. Po zakończeniu instalacji, **bramy rejestru** zostanie wyświetlone okno dialogowe dla Microsoft danych Menedżera konfiguracji bramy zarządzania. Wklej klucz rejestracji bramy, który został skopiowany do Schowka w poprzednim kroku, a następnie kliknij przycisk **zarejestrować**.

    ![Zarejestruj bramę](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfiguracja bramy została ukończona, gdy następujące wartości są ustawiane na **Home** kartę w Microsoft danych Menedżera konfiguracji bramy zarządzania:

    * **Nazwa bramy** i **nazwa wystąpienia** są ustawione na nazwy bramy.
    * **Rejestracja** ustawiono **zarejestrowanej**.
    * **Stan** ustawiono **uruchomiono**.
    * Pasek stanu u dołu Wyświetla **podłączone do usługi chmury bramy zarządzania danymi** wraz z zielonym znacznikiem wyboru.

      ![Menedżer bramy zarządzania danymi](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio również aktualizowany po pomyślnej rejestracji.

    ![Rejestracja bramy powiodło się](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. W **Pobierz i zarejestruj bramę danych** okna dialogowego, kliknij znacznik wyboru, aby ukończyć instalację. **Ustawienia** strona wyświetla stan bramy jako "W trybie Online". W okienku po prawej stronie, stanu i znajdziesz inne przydatne informacje.

    ![Ustawienia bramy](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. W programie Microsoft danych Menedżera konfiguracji bramy zarządzania przełączyć się do **certyfikatu** kartę. Certyfikat określony na tej karcie jest używana do szyfrowania/odszyfrowywania poświadczeń magazynu danych lokalnych w portalu. Ten certyfikat jest certyfikat domyślny. Firma Microsoft zaleca zmianę to swój własny certyfikat, który kopii zapasowej w systemie zarządzania certyfikatu. Kliknij przycisk **zmiany** zamiast tego użyć własnego certyfikatu.

    ![Certyfikat bramy zmiany](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcjonalnie) Jeśli chcesz włączyć pełne rejestrowanie, aby rozwiązać problemy z bramą w Microsoft danych Menedżera konfiguracji bramy zarządzania przełączyć się do **diagnostyki** i sprawdź **włączyć pełne rejestrowanie na potrzeby rozwiązywania problemów** opcji. Rejestrowanie informacji można znaleźć w Podglądzie zdarzeń systemu Windows w obszarze **Dzienniki aplikacji i usług**  - &gt; **brama zarządzania danymi** węzła. Można również użyć **diagnostyki** kartę, aby przetestować połączenie ze źródłem danych lokalnych przy użyciu bramy.

    ![Włącz pełne rejestrowanie](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Na tym kończy proces konfigurowania bramy w usłudze Azure Machine Learning.
Teraz możesz użyć danych lokalnych.

Można utworzyć i skonfigurować wiele bram w Studio dla każdego obszaru roboczego. Na przykład masz bramy, który chcesz połączyć się ze źródłami danych testu podczas tworzenia i bramę różnych źródeł danych produkcyjnych. Usługa Azure Machine Learning zapewnia elastyczność do konfigurowania wielu bram zależności w środowisku firmowym. Obecnie nie można udostępnić bramy między obszarami roboczymi i na jednym komputerze można zainstalować tylko jedną bramę. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2: Używania bramy można odczytać danych z lokalnego źródła danych
Po skonfigurowaniu bramy możesz dodać **i zaimportuj dane** modułu do eksperymentu danych wejściowych danych z lokalną bazą danych programu SQL Server.

1. W usłudze Machine Learning Studio, wybierz **EKSPERYMENTY** , kliknij pozycję **+ nowy** w lewym dolnym rogu i wybierz **pusty eksperyment** (lub wybierz jeden z kilku próbki eksperymenty dostępna).
2. Znajdź i przeciągnij **i zaimportuj dane** modułu do obszaru roboczego eksperymentu.
3. Kliknij przycisk **Zapisz jako** poniżej obszaru roboczego. Wprowadź "Azure lokalnego programu SQL Server samouczek dotyczący uczenia maszynowego" dla nazwy eksperymentu, wybierz obszar roboczy, a następnie kliknij przycisk **OK** znacznik wyboru.

   ![Zapisz doświadczenia z nową nazwą](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknij przycisk **i zaimportuj dane** moduł wybierz go, a następnie w **właściwości** w okienku po prawej stronie kanwy, wybierz "Lokalną bazą danych SQL" **źródła danych** listy rozwijanej.
5. Wybierz **bramy danych** zainstalowany i zarejestrowany. Można skonfigurować inną bramę, wybierając opcję "(Dodaj nową bramę danych...)".

   ![Wybierz bramę danych dla modułu importowanie danych](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Wprowadź SQL **nazwę serwera bazy danych** i **Nazwa bazy danych**, wraz z SQL **zapytanie bazy danych** chcesz wykonać.
7. Kliknij przycisk **wprowadź wartości** w obszarze **nazwy użytkownika i hasła** i wprowadź swoje poświadczenia bazy danych. Umożliwia zintegrowane uwierzytelnianie systemu Windows lub uwierzytelniania programu SQL Server, w zależności od konfiguracji serwera SQL lokalnie.

   ![Wprowadź poświadczenia bazy danych](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Zmiany "wymaganych wartości" komunikat "Ustaw wartości" z zielonym znacznikiem wyboru. Wystarczy raz wprowadź poświadczenia, o ile zmiany bazy danych lub hasła. Usługa Azure Machine Learning używa certyfikatu, podane podczas instalacji bramy do szyfrowania poświadczeń w chmurze. Azure nigdy nie przechowuje poświadczeń lokalnych bez szyfrowania.

   ![Importowanie właściwości modułu danych](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknij przycisk **Uruchom** uruchamianie eksperymentu.

Po zakończeniu eksperymentu można zwizualizować dane zaimportowane z bazy danych, klikając portem wyjściowym **i zaimportuj dane** modułu i wybierając **Visualize**.

Po zakończeniu tworzenia eksperymentu można wdrażać i operacjonalizować model. Korzystanie z usługi wykonywania wsadowego danych z lokalnego serwera SQL bazy danych skonfigurowanych w **i zaimportuj dane** modułu zostanie odczytu i będzie używana do oceniania. Za pomocą usługi odpowiedzi żądania dla oceniania danych lokalnych, firma Microsoft zaleca używanie [dodatek programu Excel](excel-add-in-for-web-services.md) zamiast tego. Obecnie, zapisywania do bazy danych programu SQL Server lokalnej za pośrednictwem **eksportowanie danych** nie jest obsługiwane zarówno w eksperymentów lub opublikowane w sieci web usługi.
