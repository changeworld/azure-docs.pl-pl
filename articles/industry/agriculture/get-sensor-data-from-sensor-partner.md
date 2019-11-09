---
title: Pobieranie danych z czujników partnerów
description: Opisuje sposób uzyskiwania danych czujników od partnerów
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 424df8ac7145129dd70a9a5791648730189b528b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889672"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Pobierz dane czujnika z partnerów czujników

Usługa Azure FarmBeats ułatwia przenoszenie strumieniowych danych z urządzeń IoT i czujników do centrum danych. Obecnie obsługiwane są następujące partnerzy urządzeń czujnika:

  ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Integrowanie danych urządzenia z usługą Azure FarmBeats ułatwia pobieranie danych z czujników IoT wdrożonych w farmie do centrum danych. Dane, które są dostępne, można wizualizować za pośrednictwem akceleratora FarmBeats i mogą być używane na potrzeby łączenia danych i modeli AI/ML przy użyciu FarmBeats.

Aby rozpocząć przesyłanie strumieniowe danych z czujnika, upewnij się, że:

-  Zainstalowano FarmBeats z witryny Azure Marketplace.
-  Użytkownik zdecydował się na czujników i urządzeniach, które mają zostać zainstalowane w farmie.
-  Jeśli planujesz korzystanie z czujników wilgotności gleby, można użyć mapy położenia czujnika FarmBeats gleby, aby uzyskać zalecenie dotyczące liczby czujników i gdzie dokładnie powinny zostać umieszczone czujniki. Aby uzyskać więcej informacji, zobacz [generowanie map](generate-maps.md).

- Kup i Wdróż urządzenia/czujniki od partnera urządzenia w farmie. Upewnij się, że masz dostęp do danych czujników za pośrednictwem rozwiązania partnerów urządzeń.

### <a name="enable-device-integration-with-farmbeats"></a>Włącz integrację urządzeń z usługą FarmBeats   

Po rozpoczęciu przesyłania strumieniowego danych czujników można rozpocząć proces uzyskiwania danych do systemu FarmBeats. Aby umożliwić integrację z usługą FarmBeats, należy podać następujące informacje dla dostawcy urządzeń:  

 - Punkt końcowy interfejsu API  
 - Identyfikator dzierżawy  
 - Identyfikator klienta  
 - Klucz tajny klienta  
 - Parametry połączenia EventHub

Powyższe informacje są udostępniane przez Integrator systemu. W przypadku problemów związanych z integracją urządzeń należy skontaktować się z integratorem systemu.

Alternatywnie można wygenerować poświadczenia, uruchamiając ten skrypt z Azure Cloud Shell. Wykonaj poniższe czynności:

1. Pobierz [plik zip](https://aka.ms/farmbeatspartnerscript) i wyodrębnij go na dysk lokalny. W pliku ZIP znajdują się dwa pliki.
2. Zaloguj się do https://portal.azure.com/ i Otwórz Cloud Shell (Ta opcja jest dostępna w prawym górnym pasku portalu)  

    ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Upewnij się, że środowisko jest ustawione na program **PowerShell** — domyślnie jest ono ustawione na bash.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Przekaż dwa pliki (z kroku 1 powyżej) do Cloud Shell.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Przejdź do katalogu, do którego pliki zostały przekazane (domyślnie są przekazywane do katalogu macierzystego > nazwa użytkownika).
6. Uruchom następujący skrypt:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Postępuj zgodnie z instrukcjami na ekranie, aby przechwycić wartości. (Punkt końcowy interfejsu API, identyfikator dzierżawy, identyfikator klienta, klucz tajny klienta i parametry połączenia EventHub).

**Integrowanie danych urządzenia przy użyciu wygenerowanych poświadczeń**

Odwiedź portal partnera urządzeń, aby utworzyć link FarmBeats za pomocą zestawu poświadczeń wygenerowanych w poprzedniej sekcji.

 - Punkt końcowy interfejsu API  
 - Parametry połączenia EventHub  
 - Identyfikator klienta  
 - Klucz tajny klienta  
 - Identyfikator dzierżawy  

 Dostawca urządzenia potwierdza pomyślną integrację. Po potwierdzeniu można wyświetlić wszystkie urządzenia i czujniki w usłudze Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Wyświetlanie urządzeń i czujników

Skorzystaj z poniższej sekcji, aby wyświetlić urządzenia i czujniki w farmie.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Obecnie FarmBeats obsługuje następujące urządzenia:

- **Węzeł**: urządzenie, do którego jest dołączona co najmniej jedna czujników.
- **Brama**: urządzenie, do którego jest dołączona co najmniej jedna z czujników.

Wykonaj następujące czynności:

1. Na stronie głównej wybierz pozycję **urządzenia** z menu.
  Na stronie urządzenia jest wyświetlany typ urządzenia, model, stan, Farma, w której znajduje się, oraz Data ostatniej aktualizacji metadanych. Domyślnie kolumna Farma ma wartość NULL. Możesz przypisać urządzenie do farmy. Aby uzyskać więcej informacji, zobacz [przypisywanie urządzeń](#assign-devices).
2. Wybierz urządzenie, aby wyświetlić właściwości urządzenia, dane telemetryczne i urządzenia podrzędne połączone z urządzeniem.  

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Wyświetl czujniki

Wykonaj następujące czynności:

1. Na stronie głównej wybierz z menu pozycję **czujniki** .
  Na stronie czujników są wyświetlane szczegółowe informacje dotyczące typu czujnika, farmy, z którą jest ona połączona, urządzenie nadrzędne, nazwa portu, typ portu i ostatni zaktualizowany stan.
2. Wybierz czujnik, aby wyświetlić właściwości czujnika, aktywne alerty i dane telemetryczne z czujnika.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Przypisywanie urządzeń  

Po przepływaniu danych z czujnika w programie można przypisać go do farmy, w której wdrożono czujniki.

1. Na stronie głównej wybierz pozycję **farmy** z menu, zostanie wyświetlona strona listy **farmy** .  
2. Wybierz farmę, do której chcesz przypisać urządzenie, a następnie wybierz pozycję **Dodaj urządzenia**.  
3. Zostanie wyświetlone okno **Dodawanie urządzeń** . Wybierz urządzenie, które chcesz przypisać do farmy.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Wybierz pozycję **Dodaj urządzenia**. Alternatywnie przejdź do menu **urządzenia** , wybierz urządzenia, które chcesz przypisać do farmy, a następnie wybierz pozycję **Skojarz urządzenia**.  
5. W oknie **kojarzenie urządzeń** wybierz farmę z listy rozwijanej i wybierz pozycję **Zastosuj do wszystkich** , aby skojarzyć farmę ze wszystkimi wybranymi urządzeniami.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Aby skojarzyć każde urządzenie z inną farmą, wybierz listę rozwijaną w kolumnie **Przypisz do farmy** i wybierz farmę dla każdego wiersza urządzenia.  
7. Wybierz pozycję **Przypisz** , aby zakończyć przypisanie urządzenia.

### <a name="visualize-sensor-data"></a>Wizualizuj dane czujnika

Wykonaj następujące czynności:

1. Na stronie głównej wybierz pozycję **farmy** z menu, aby wyświetlić stronę **farmy** .  
2. Wybierz **farmę** , dla której chcesz zobaczyć dane czujnika.  
3. Na pulpicie nawigacyjnym **farmy** można wyświetlić dane telemetryczne. Możesz wybrać wyświetlanie telemetrii na żywo lub użyć **zakresu niestandardowego** do wyświetlania w określonym zakresie dat.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Usuń czujnik

Wykonaj następujące kroki:

1. Na stronie głównej wybierz z menu pozycję **czujniki** , aby wyświetlić stronę **czujników** .  
2. Wybierz urządzenie, które chcesz usunąć, i wybierz pozycję **Usuń** z okna potwierdzenia.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Zostanie wyświetlony komunikat z potwierdzeniem, że czujnik został pomyślnie usunięty.  

## <a name="delete-devices"></a>Usuwanie urządzeń

Wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **urządzenia** z menu, aby wyświetlić stronę urządzenia.  
2. Wybierz urządzenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń** z okna potwierdzenia.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Następne kroki

Teraz dane czujnika są przepływane do wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [generować mapy](generate-maps.md#generate-maps) dla Farm.
