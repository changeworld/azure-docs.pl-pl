---
title: Pobieranie danych z czujników partnerów
description: W tym artykule opisano, jak uzyskać dane czujnika z partnerów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d56504c96c5e039f2563a1bfee577fe9b15e8563
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715576"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Pobierz dane czujnika z partnerów czujników

Usługa Azure FarmBeats ułatwia przenoszenie strumieniowych danych z urządzeń IoT i czujników do Datahub. Obecnie obsługiwane są następujące partnerzy urządzeń czujnika.

  ![Partnerzy FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Integrowanie danych urządzenia z usługą Azure FarmBeats ułatwia pobieranie danych z czujników IoT wdrożonych w farmie do centrum danych. Dane, które są dostępne, można wizualizować za pomocą akceleratora FarmBeats. Dane mogą służyć do kompilowania modelu i uczenia maszynowego/sztucznej analizy danych (ML/AI) przy użyciu FarmBeats.

Aby rozpocząć przesyłanie strumieniowe danych z czujnika, upewnij się, że:

-  Zainstalowano FarmBeats w portalu Azure Marketplace.
-  Użytkownik zdecydował się na czujników i urządzeniach, które mają zostać zainstalowane w farmie.
-  Jeśli planujesz korzystanie z czujników odnoszących się do wilgotności gleby, Użyj mapy umieszczenia czujnika wilgoci FarmBeats, aby uzyskać zalecenie dotyczące liczby czujników i dokładnie je umieścić. Aby uzyskać więcej informacji, zobacz [generowanie map](generate-maps-in-azure-farmbeats.md).
- Możesz zakupić i wdrożyć urządzenia lub czujniki od partnera urządzenia w farmie. Upewnij się, że masz dostęp do danych czujników za pośrednictwem rozwiązania partnerów urządzeń.

## <a name="enable-device-integration-with-farmbeats"></a>Włącz integrację urządzeń z usługą FarmBeats

Po rozpoczęciu przesyłania strumieniowego danych czujników można rozpocząć proces uzyskiwania danych do systemu FarmBeats. Podaj następujące informacje dla dostawcy urządzenia, aby umożliwić integrację z usługą FarmBeats:

 - Punkt końcowy interfejsu API
 - Identyfikator dzierżawy
 - Identyfikator klienta
 - Klucz tajny klienta
 - Parametry połączenia EventHub

Powyższe informacje można wygenerować, wykonując następujące czynności: (należy pamiętać, że te kroki muszą zostać wykonane na platformie Azure, aby uzyskać dostęp do subskrypcji platformy Azure, w której wdrożono FarmBeats).

1. Pobierz [plik zip](https://aka.ms/farmbeatspartnerscriptv2)i wyodrębnij go na dysk lokalny. Plik zip będzie zawierać jeden plik.
2. Zaloguj się do https://portal.azure.com/ i przejdź do pozycji Azure Active Directory-> rejestracji aplikacji

3. Kliknij rejestrację aplikacji, która została utworzona w ramach wdrożenia FarmBeats. Będzie ona mieć taką samą nazwę jak centrum danych FarmBeats.

4. Kliknij pozycję "Uwidacznianie interfejsu API" — > kliknij pozycję "Dodaj aplikację kliencką" i wprowadź **04b07795-8ddb-461a-bbee-02f9e1bf7b46** i zaznacz pole wyboru Autoryzuj zakres. Zapewni to dostęp do interfejsu wiersza polecenia platformy Azure (Cloud Shell), aby wykonać poniższe kroki.

5. Otwórz usługę Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu Azure Portal.

    ![Azure Portal pasek narzędzi](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Upewnij się, że środowisko jest ustawione na **PowerShell**. Domyślnie jest ono ustawione na bash.

    ![Ustawienie paska narzędzi programu PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Przekaż plik z kroku 1 w wystąpieniu Cloud Shell.

    ![Przekaż przycisk paska narzędzi](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Przejdź do katalogu, w którym plik został przekazany. Domyślnie pliki są przekazywane do katalogu macierzystego w ramach nazwy użytkownika.

9. Uruchom następujący skrypt. Skrypt pyta o identyfikator dzierżawy, który można uzyskać ze strony Przegląd Azure Active Directory->.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Postępuj zgodnie z instrukcjami na ekranie, aby przechwycić wartości dla **punktów końcowych interfejsu API**, **identyfikatora dzierżawy**, **identyfikatora klienta**, **klucza tajnego klienta**i **parametrów połączenia centrum EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrowanie danych urządzenia przy użyciu wygenerowanych poświadczeń

Teraz masz następujące informacje, które zostały wygenerowane w poprzedniej sekcji.
 - Punkt końcowy interfejsu API
 - Parametry połączenia EventHub
 - Identyfikator klienta
 - Klucz tajny klienta
 - Identyfikator dzierżawy
 
Musisz podać to partnerowi urządzenia do łączenia FarmBeats. Przejdź do portalu partnera urządzeń, aby to zrobić. Na przykład w przypadku używania urządzeń z instrumentów Davis przejdź do poniższej strony:

[Instrumenty Davis](https://weatherlink.github.io/azure-farmbeats/setup)

 Dostawca urządzenia potwierdza pomyślną integrację. Po potwierdzeniu można wyświetlić wszystkie urządzenia i czujniki w usłudze Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Wyświetlanie urządzeń i czujników

Skorzystaj z poniższej sekcji, aby wyświetlić urządzenia i czujniki dla farmy.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Obecnie FarmBeats obsługuje następujące urządzenia:

- **Węzeł**: urządzenie, do którego dołączono co najmniej jedną czujników.
- **Brama**: urządzenie, do którego dołączono co najmniej jeden węzeł.

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **urządzenia** z menu.
  Na stronie **urządzenia** jest wyświetlany typ urządzenia, model, stan, Farma, w której znajduje się, oraz Data ostatniej aktualizacji metadanych. Domyślnie kolumna Farma ma *wartość null*. Możesz przypisać urządzenie do farmy. Aby uzyskać więcej informacji, zobacz [przypisywanie urządzeń](#assign-devices).
2. Wybierz urządzenie, aby wyświetlić właściwości, dane telemetryczne i urządzenia podrzędne połączone z urządzeniem.

    ![Strona urządzenia](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Wyświetl czujniki

Wykonaj następujące kroki.

1. Na stronie głównej wybierz z menu pozycję **czujniki** .
  Na stronie **czujników** są wyświetlane szczegółowe informacje o typie czujnika, farmie, z którym jest połączona, urządzeniu nadrzędnym, nazwie portu, typie portu i ostatnim zaktualizowanym stanem.
2. Wybierz czujnik, aby wyświetlić właściwości czujnika, aktywne alerty i dane telemetryczne z czujnika.

    ![Strona czujników](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Przypisywanie urządzeń  

Po przejściu danych z czujnika w programie można przypisać go do farmy, w której zostały wdrożone czujniki.

1. Na stronie głównej wybierz z menu pozycję **farmy** . Zostanie wyświetlona strona listy **farmy** .
2. Wybierz farmę, do której chcesz przypisać urządzenie, a następnie wybierz pozycję **Dodaj urządzenia**.
3. Zostanie wyświetlone okno **Dodawanie urządzeń** . Wybierz urządzenie, które chcesz przypisać do farmy.

    ![Okno dodawania urządzeń](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Wybierz pozycję **Dodaj urządzenia**. Alternatywnie przejdź do menu **urządzenia** , wybierz urządzenia, które chcesz przypisać do farmy, a następnie wybierz pozycję **Skojarz urządzenia**.
5. W oknie **kojarzenie urządzeń** wybierz farmę z listy rozwijanej, a następnie wybierz pozycję **Zastosuj do wszystkich** , aby skojarzyć farmę ze wszystkimi wybranymi urządzeniami.

    ![Okno kojarzenia urządzeń](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Aby skojarzyć każde urządzenie z inną farmą, wybierz strzałkę listy rozwijanej w kolumnie **Przypisz do farmy** , a następnie wybierz farmę dla każdego wiersza urządzenia.
7. Wybierz pozycję **Przypisz** , aby zakończyć przypisanie urządzenia.

### <a name="visualize-sensor-data"></a>Wizualizuj dane czujnika

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **farmy** z menu, aby wyświetlić stronę **farmy** .
2. Wybierz **farmę** , dla której chcesz zobaczyć dane czujnika.
3. Na pulpicie nawigacyjnym **farmy** można wyświetlić dane telemetryczne. Możesz wyświetlić dane telemetryczne na żywo lub użyć **zakresu niestandardowego** , aby zobaczyć konkretny zakres dat.

    ![Pulpit nawigacyjny farmy](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Usuwanie czujnika

Wykonaj następujące kroki.

1. Na stronie głównej wybierz z menu pozycję **czujniki** , aby wyświetlić stronę **czujników** .
2. Wybierz urządzenie, które chcesz usunąć, a następnie w oknie potwierdzenia wybierz pozycję **Usuń** .

    ![Przycisk Usuń](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Zostanie wyświetlony komunikat z potwierdzeniem, że czujnik został pomyślnie usunięty.

## <a name="delete-devices"></a>Usuwanie urządzeń

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **urządzenia** z menu, aby wyświetlić stronę **urządzenia** .
2. Wybierz urządzenie, które chcesz usunąć, a następnie w oknie potwierdzenia wybierz pozycję **Usuń** .

    ![Przycisk Usuń](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Następne kroki

Teraz dane czujnika są przepływane do wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla Farm.
