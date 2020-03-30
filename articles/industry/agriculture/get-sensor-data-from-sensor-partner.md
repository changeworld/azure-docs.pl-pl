---
title: Uzyskaj dane z czujników od partnerów
description: W tym artykule opisano sposób utrzymywać dane z czujników od partnerów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384198"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Uzyskaj dane z czujników od partnerów czujników

Usługa Azure FarmBeats ułatwia przesyłanie danych strumieniowych z urządzeń IoT i czujników do usługi Datahub. Obecnie obsługiwane są następujące partnerów urządzeń czujnikowych.

  ![Partnerzy FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Integracja danych urządzenia z usługą Azure FarmBeats ułatwia uzyskanie danych naziemnych z czujników IoT wdrożonych w farmie do centrum danych. Dane, po udostępnieniu, można wizualizować za pomocą akceleratora FarmBeats. Dane mogą być używane do fuzji danych i uczenia maszynowego /sztucznej inteligencji (ML/AI) tworzenia modelu przy użyciu FarmBeats.

Aby rozpocząć przesyłanie strumieniowe danych z czujnika, upewnij się, że:

-  Zainstalowano FarmBeats w portalu Azure Marketplace.
-  Zdecydowałeś się na czujniki i urządzenia, które chcesz zainstalować w swojej farmie.
-  Jeśli planujesz korzystanie z czujników wilgotności gleby, skorzystaj z mapy Umieszczania czujników wilgotności gleby FarmBeats, aby uzyskać zalecenie dotyczące liczby czujników i miejsca, w którym dokładnie należy je umieścić. Aby uzyskać więcej informacji, zobacz [Generowanie map](generate-maps-in-azure-farmbeats.md).
- Kupujesz i wdrażasz urządzenia lub czujniki od partnera urządzenia w swojej farmie. Upewnij się, że masz dostęp do danych czujnika za pośrednictwem rozwiązania partnerów urządzenia.

## <a name="enable-device-integration-with-farmbeats"></a>Włącz integrację urządzenia z FarmBeats

Po uruchomieniu przesyłania strumieniowego danych z czujników można rozpocząć proces uzyskiwania danych do systemu FarmBeats. Podaj następujące informacje do dostawcy urządzenia, aby włączyć integrację z FarmBeats:

 - Punkt końcowy interfejsu API
 - Identyfikator dzierżawy
 - Identyfikator klienta
 - Klucz tajny klienta
 - Parametry połączenia EventHub

Możesz wygenerować powyższe informacje, wykonując następujące kroki: (Należy pamiętać, że te kroki muszą być wykonane na platformie Azure, więc będziesz potrzebować dostępu do subskrypcji platformy Azure, w której wdrożono FarmBeats)

1. Zaloguj się do witryny https://portal.azure.com/.

2. **Jeśli korzystasz z FarmBeats w wersji 1.2.7 lub nowszej, pomiń kroki 2a, 2b i 2c i przejdź do kroku 3.**. Wersję FarmBeats można sprawdzić, klikając ikonę Ustawienia w prawym górnym rogu interfejsu FarmBeats.

2a. Przejdź do rejestracji aplikacji usługi Azure Active Directory >

2b. Kliknij rejestrację aplikacji, która została utworzona w ramach wdrożenia FarmBeats. Będzie miał taką samą nazwę jak Centrum danych FarmBeats.

2c. Kliknij na "Expose API" -> Kliknij "Dodaj aplikację kliencką" i wprowadź **04b07795-8ddb-461a-bbee-02f9e1bf7b46** i zaznacz opcję "Zakres autoryzowania". Spowoduje to udzielenie dostępu do interfejsu wiersza polecenia platformy Azure (Cloud Shell) w celu wykonania poniższych kroków.

3. Otwórz usługę Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu witryny Azure portal.

    ![Pasek narzędzi portalu Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. Upewnij się, że środowisko jest ustawione na **program PowerShell**. Domyślnie jest ustawiona na Bash.

    ![Ustawienie paska narzędzi programu PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. Przejdź do katalogu domowego.

   ```azurepowershell-interactive 

    cd  

    ```

7. Uruchom następujące polecenie. Spowoduje to pobranie skryptu do katalogu macierzystego.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. Uruchom następujący skrypt. Skrypt prosi o identyfikator dzierżawy, który można uzyskać ze strony Przegląd usługi Azure Active Directory ->.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby przechwycić wartości **dla punktu końcowego interfejsu API,** **identyfikatora dzierżawy,** **identyfikatora klienta,** **klucza tajnego klienta**i ciągu połączenia **EventHub.**

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrowanie danych urządzenia przy użyciu wygenerowanych poświadczeń

Teraz masz następujące informacje wygenerowane z poprzedniej sekcji.
 - Punkt końcowy interfejsu API
 - Parametry połączenia EventHub
 - Identyfikator klienta
 - Klucz tajny klienta
 - Identyfikator dzierżawy
 
Musisz dostarczyć to partnerowi urządzenia do łączenia FarmBeats. Przejdź do portalu partnera urządzenia, aby zrobić to samo. Na przykład, w przypadku korzystania z urządzeń z Davis Instruments, Teralytic lub Pessl Instruments (Metos.at) przejdź do odpowiednich stron, jak wspomniano poniżej:

[Instrumenty Davisa](https://weatherlink.github.io/azure-farmbeats/setup)

[Teralityczny](https://app.teralytic.com/)

[Instrumenty Pessl](https://ng.fieldclimate.com/user-api-services)

 Dostawca urządzeń potwierdza pomyślną integrację. Po potwierdzeniu można wyświetlić wszystkie urządzenia i czujniki na platformie Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Wyświetlanie urządzeń i czujników

Skorzystaj z poniższej sekcji, aby wyświetlić urządzenia i czujniki dla twojej farmy.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Obecnie FarmBeats obsługuje następujące urządzenia:

- **Węzeł**: Urządzenie, do którego podłączony jest jeden lub więcej czujników.
- **Brama:** Urządzenie, do którego jest podłączony jeden lub więcej węzłów.

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **Urządzenia** z menu.
  Strona **Urządzenia** wyświetla typ urządzenia, model, stan, farmę, w którym została umieszczona, oraz ostatnią zaktualizowaną datę metadanych. Domyślnie kolumna farmy jest *ustawiona*na wartość NULL . Można przypisać urządzenie do farmy. Aby uzyskać więcej informacji, zobacz [Przypisywanie urządzeń](#assign-devices).
2. Wybierz urządzenie, aby wyświetlić właściwości urządzenia, dane telemetryczne i urządzenia podrzędne podłączone do urządzenia.

    ![Strona Urządzenia](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Zobacz czujniki

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **Czujniki** z menu.
  Na stronie **Czujniki** są wyświetlane szczegółowe informacje o typie czujnika, farmie, z jaką jest podłączona, urządzeniu nadrzędnym, nazwie portu, typie portu i ostatnim zaktualizowanym stanie.
2. Wybierz czujnik, aby wyświetlić właściwości czujnika, aktywne alerty i dane telemetryczne z czujnika.

    ![Strona czujników](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Przypisywanie urządzeń  

Po wczycie danych z czujnika można przypisać je do farmy, w której wdrożono czujniki.

1. Na stronie głównej wybierz z menu **pozycję Farmy.** Zostanie wyświetlona strona listy **Farmy.**
2. Wybierz farmę, do której chcesz przypisać urządzenie, a następnie wybierz pozycję **Dodaj urządzenia**.
3. Zostanie wyświetle okno **Dodaj urządzenia.** Wybierz urządzenie, które chcesz przypisać do farmy.

    ![Okno Dodawanie urządzeń](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Wybierz **pozycję Dodaj urządzenia**. Możesz też przejść do menu **Urządzenia,** wybrać urządzenia, które chcesz przypisać do farmy, i wybierz polecenie **Skojarz urządzenia**.
5. W oknie **Skojarz urządzenia** wybierz farmę z listy rozwijanej i wybierz pozycję Zastosuj **do wszystkich,** aby skojarzyć farmę ze wszystkimi wybranymi urządzeniami.

    ![Okno Skojarz urządzenia](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Aby skojarzyć każde urządzenie z inną farmą, wybierz strzałkę rozwijaną w kolumnie **Przypisz do farmy** i wybierz farmę dla każdego wiersza urządzenia.
7. Wybierz **pozycję Przypisz,** aby ukończyć przypisanie urządzenia.

### <a name="visualize-sensor-data"></a>Wizualizowanie danych czujników

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **Farmy** z menu, aby wyświetlić stronę **Farmy.**
2. Wybierz **farmę,** dla której chcesz wyświetlić dane z czujnika.
3. Na **pulpicie** nawigacyjnym farmy można wyświetlać dane telemetryczne. Można wyświetlić dane telemetryczne na żywo lub użyć **zakresu niestandardowego,** aby wyświetlić określony zakres dat.

    ![Deska rozdzielcza farmy](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Usuwanie czujnika

Wykonaj następujące kroki.

1. Na stronie głównej wybierz **pozycję Czujniki** z menu, aby wyświetlić stronę **Czujniki.**
2. Wybierz urządzenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń** w oknie potwierdzenia.

    ![Przycisk Usuń](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Komunikat potwierdzający pokazuje, że czujnik został pomyślnie usunięty.

## <a name="delete-devices"></a>Usuwanie urządzeń

Wykonaj następujące kroki.

1. Na stronie głównej wybierz pozycję **Urządzenia** z menu, aby wyświetlić stronę **Urządzenia.**
2. Wybierz urządzenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń** w oknie potwierdzenia.

    ![Przycisk Usuń](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Następne kroki

Masz teraz dane z czujników przepływających do wystąpienia Usługi Azure FarmBeats. Teraz dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla swoich farm.
