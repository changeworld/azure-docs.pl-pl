---
title: Samouczek, aby przygotować środowisko platformy Azure portal, centrum danych do wdrożenia usługi Azure Data Box Edge | Dokumentacja firmy Microsoft
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Edge obejmuje przygotowywanie witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d7e66970db3397531c798bc37bf7c1f346e999bf
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924769"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Samouczek: Przygotowywanie do wdrażania usługi Azure Data Box Edge  


To jest pierwszy samouczek z serii samouczków dotyczących wdrażania, wymaganych do pełnego wdrożenia usługi Azure Data Box Edge. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrożenia zasobu usługi Data Box Edge.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


### <a name="get-started"></a>Rozpoczęcie pracy

Proces wdrażania usługi Data Box Edge opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Edge](data-box-edge-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Edge, zanim zainstalujesz urządzenie fizyczne Data Box Edge. |
| 2. |**[Instalowanie urządzenia Data Box Edge](data-box-edge-deploy-install.md)**|Rozpakuj, umieść na stojaku i podłącz kable urządzenia fizycznego Data Box Edge.  |
| 3. |**[Łączenie, konfigurowanie i aktywowanie urządzenia Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
| 5. |**[Przekształcanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Skonfiguruj moduły usługi Edge na urządzeniu w celu przekształcania danych podczas ich przenoszenia na platformę Azure. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Edge, urządzenia Data Box Edge i sieci centrum danych.

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

- Twoja subskrypcja platformy Microsoft Azure obsługuje zasób usługi Data Box Edge. Płatność za rzeczywiste użycie subskrypcje nie są obsługiwane.
- Masz właściciela lub współautora dostęp do Twojej subskrypcji.
- Masz administratora lub użytkownika, dostęp do interfejsu API usługi Azure Active Directory Graph. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Active Directory Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-edge-device"></a>Urządzenie Data Box Edge

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Przejrzeniu informacji bezpieczeństwa, która została uwzględniona w pakiecie wydania.
- Na standardowym stojaku 19” w centrum danych masz dostępne gniazdo 1U w celu zamontowania urządzenia. 
- Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
- Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Masz dostęp do urządzenia fizycznego.


### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia Data Box Edge. Aby uzyskać więcej informacji, zobacz [wymagania systemowe usługi Data Box Edge](data-box-edge-system-requirements.md).

- W normalnych warunkach pracy krawędzi pola danych masz:

    - Co najmniej 10 MB/s Pobierz przepustowość tak, aby upewnić się, że urządzenie jest w pełni zaktualizowany.
    - Co najmniej 20 MB/s dedykowanych przekazywanie i pobieranie przepustowości do transferu plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Edge, którego możesz użyć do zarządzania urządzeniem fizycznym, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób usługi Data Box Edge, wykonaj poniższe czynności w witrynie Azure Portal.

1. Użyj swoich poświadczeń Microsoft Azure do logowania się na 
    
    - Witryny Azure portal pod tym adresem URL: [ https://portal.azure.com ](https://portal.azure.com).
    - Lub, w portalu Azure dla instytucji rządowych pod tym adresem URL: [ https://portal.azure.us ](https://portal.azure.us). Aby uzyskać więcej informacji, przejdź do [nawiązywanie połączenia z platformy Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W okienku po lewej stronie wybierz **+ Utwórz zasób**. Wyszukaj **Data Box Edge / Data Box bramy**. Wybierz **Data Box Edge / Data Box bramy**. Wybierz pozycję **Utwórz**.
3. Wybierz subskrypcję, dla której chcesz użyć dla tego urządzenia krawędź pola danych. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. W tej wersji są dostępne wschodnie stany USA, Azja południowo-wschodnia i Europa Zachodnia. 

    Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Region przechowuje trafiają tam jedynie metadane do zarządzania urządzeniami. Rzeczywiste dane mogą być przechowywane w wszystkich kont magazynu. 
    
    W opcji usługi **Data Box Edge** wybierz pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na **podstawy** karcie wpisz lub wybierz następujące pozycje **projektu szczegóły**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |To jest automatycznie wypełniane na podstawie wybranych wcześniej. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Wprowadź lub wybierz następujące pozycje **wystąpienia szczegóły**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region     |W tej wersji są dostępne do wdrożenia usługi resource wschodnie stany USA, Azja południowo-wschodnia i Europa Zachodnia. Jeśli używasz platformy Azure Government, wszystkie regiony dla instytucji rządowych są dostępne, jak pokazano na [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Szczegóły projektu i wystąpienia](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Wybierz opcję **Dalej: Adres wysyłkowy**.

    - Jeśli masz już urządzenia, zaznacz pole kombi **mam urządzenie krawędzi pola danych**.
    - Jeśli jest to nowe urządzenie, które są kolejność, wprowadź nazwisko osoby kontaktowej, firmy, adres odesłanie urządzenia i informacje kontaktowe.

    ![Adres wysyłkowy dla nowego urządzenia](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Wybierz opcję **Dalej: Przeglądanie + tworzenie**.

7. Na **Przejrzyj + Utwórz** kartę, przejrzyj **szczegóły cennika**, **warunki użytkowania**oraz szczegóły zasobu bazy danych. Zaznacz pole kombi **przejrzeć postanowienia dotyczące prywatności**.

    ![Przejrzyj szczegóły zasobu krawędź pola danych i zasadami zachowania poufności informacji](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Wybierz pozycję **Utwórz**.

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu, otrzymasz powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu krawędź pola danych](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Po umieszczeniu kolejności, firma Microsoft przeglądów kolejności i łączy się z usługą (za pomocą poczty e-mail) przy użyciu szczegóły dotyczące wysyłki.

![Powiadomienie dla Przejrzyj zamówienia krawędź pola danych](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu usługi Data Box Edge konieczne będzie uzyskanie klucza aktywacji. Ten klucz jest używany do aktywowania urządzenia Data Box Edge i nawiązania połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz zasób, który został utworzony. Wybierz **Przegląd** , a następnie wybierz **konfiguracji urządzenia**.

    ![Wybierz konfigurację urządzenia](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na **Aktywuj** kafelka, wybierz opcję **Wygeneruj klucz** można utworzyć klucza aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować urządzenie Data Box Edge.

> [!div class="nextstepaction"]
> [Instalowanie urządzenia Data Box Edge](./data-box-edge-deploy-install.md)



