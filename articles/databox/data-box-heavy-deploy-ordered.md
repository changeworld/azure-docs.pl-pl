---
title: Samouczek przedstawiający kolejność Azure Data Box Heavy | Microsoft Docs
description: Zapoznaj się z wymaganiami wstępnymi dotyczącymi wdrażania i kolejnością Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241404"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Samouczek: Azure Data Box Heavy kolejności


Azure Data Box Heavy to rozwiązanie hybrydowe, które pozwala na szybkie i łatwe importowanie danych lokalnych do platformy Azure. Dane są przesyłane do urządzenia magazynującego dostarczonego przez firmę Microsoft o pojemności 770 TB (w przybliżeniu możliwego do użycia), a następnie do ponownego wysłania urządzenia. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano, jak można zamówić Azure Data Box Heavy. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące Data Box Heavy
> * Zamówienie Data Box Heavy
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z usługą i urządzeniem Data Box.

### <a name="for-installation-site"></a>Lokacja instalacji

Przed rozpoczęciem upewnij się, że:

- Urządzenie jest zgodne ze standardami Doorways i entryways. Należy jednak upewnić się, że urządzenie może być zgodne ze wszystkimi entryways. Wymiary urządzenia: Szerokość: 26 "Długość: 48 "Wysokość: 28 ".
- Jeśli jest zainstalowany w piętru innym niż piętro podłogowe, potrzebny jest dostęp do urządzenia za pośrednictwem windy lub rampy. Urządzenie odważy około 500 funtów.
- Upewnij się, że w centrum danych istnieje płaska lokacja z bliskością dostępnego połączenia sieciowego, które może obsłużyć urządzenie z tym ustawieniem.


### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Upewnij się, że masz właściciela lub dostęp współautora do subskrypcji, aby utworzyć zamówienie Data Box Heavy.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:
- Urządzenie zostało rozpakowane.
- Należy posiadać komputer hosta podłączony do sieci centrum danych. Data Box Heavy skopiuje dane z tego komputera. Na komputerze hosta musi być uruchomiony obsługiwany system operacyjny, zgodnie z opisem w temacie [wymagania systemowe Azure Data Box Heavy](data-box-system-requirements.md).
- Aby połączyć się z lokalnym interfejsem użytkownika i skonfigurować urządzenie, należy mieć komputer przenośny z kablem RJ-45. Aby skonfigurować każdy węzeł urządzenia, użyj laptopa.
- Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE.
- Potrzebny jest 1 40 GB/s lub kabel 10 GB/s dla każdego węzła urządzenia. Wybierz kable, które są zgodne z interfejsem sieciowym [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) :

    - W przypadku kabla 40 GB/s urządzenie musi mieć wartość QSFP +.
    - W przypadku kabla 10 GB/s potrzebny jest kabel SFP +, który jest podłączany do 10 G przełącznika na jednym końcu, przy użyciu karty QSFP + do SFP + adapter (lub karty QSA) dla punktu końcowego, który jest podłączony do urządzenia.
    - Kable zasilające są dołączone do urządzenia.

## <a name="order-data-box-heavy"></a>Data Box Heavy kolejności

Wykonaj poniższe czynności w witrynie Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób** i Wyszukaj *Azure Data Box*. Wybierz **Azure Data Box**.
    
   [![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Wybierz pozycję **Utwórz**.

4. Sprawdź, czy usługa urządzenie Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje i wybierz pozycję **Zastosuj**.

    |Ustawienie  |Value  |
    |---------|---------|
    |Subscription     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Kraj źródłowy     | Wybierz kraj/region, w którym znajdują się obecnie dane.         |
    |Docelowy region platformy Azure     | Wybierz region platformy Azure, do którego chcesz przenieść dane.        |

    [![Wybierz dostępność rodziny urządzenie Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Wybierz **Data Box Heavy**. Maksymalna użyteczna pojemność dla pojedynczej kolejności wynosi 770 TB.

    [![Wybierz Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **dalej**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name     | Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |
    |Resource group     | Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Docelowy region platformy Azure     | Wybierz region swojego konta magazynu. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Magazyn docelowy     | Wybierz opcję z konta magazynu lub dysków zarządzanych lub obu. <br> W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. <br>Data Box Heavy można łączyć z maksymalnie 10 kontami magazynu. <br> Możesz również utworzyć nowe konto **ogólnego przeznaczenia w wersji 1**, w **wersji 2**lub na potrzeby **magazynu obiektów BLOB**. <br> Azure Data Lake Storage konta generacji 2 nie są obsługiwane. Zobacz [konta magazynu obsługiwane z urządzeniem](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze urządzenie Data Box współpracują z bezpiecznymi kontami magazynu, należy włączyć usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz jak [dodać usługę Azure Data Box jako zaufaną usługę](../storage/common/storage-network-security.md#exceptions).|

    W przypadku używania konta magazynu jako miejsca docelowego magazynu zostanie wyświetlony następujący zrzut ekranu:

    ![Zamówienie Data Box Heavy dla konta magazynu](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Jeśli oprócz konta magazynu jako miejsca docelowego magazynu używa się również Data Box Heavy do tworzenia dysków zarządzanych na podstawie lokalnych dysków VHD, należy podać następujące informacje:

    |Ustawienie  |Value  |
    |---------|---------|
    |Grupy zasobów     | Utwórz nowe grupy zasobów, jeśli zamierzasz tworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Istniejącej grupy zasobów można użyć tylko wtedy, gdy grupa zasobów została utworzona wcześniej podczas tworzenia kolejności Data Box Heavy dla dysku zarządzanego przez usługę urządzenie Data Box. <br> Określ wiele grup zasobów rozdzielonych średnikami. Obsługiwane są maksymalnie 10 grup zasobów.|

    ![Kolejność Data Box Heavy dla dysku zarządzanego](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla usługi Managed disks jest używane jako tymczasowe konto magazynu. Usługa urządzenie Data Box przekazuje wirtualne dyski twarde jako stronicowe obiekty blob do konta magazynu tymczasowego przed przekonwertowaniem go na dyski zarządzane i przenieść je do grup zasobów. Aby uzyskać więcej informacji, zobacz [Sprawdzanie przekazywania danych na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Wybierz pozycję **Weryfikuj adres**. 

    Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. Wybierz opcję **Dalej**.

8. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

9. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

10. Wybierz pozycję **Zamówienie**. Utworzenie zamówienia trwa kilka minut.


## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do kolejności Data Box Heavy, a następnie przejdź do pozycji **Przegląd** , aby wyświetlić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Jeśli urządzenie nie jest niedostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

- Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
- Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
- Generowane jest również hasło urządzenia, które pomaga odblokować urządzenie.
- Data Box Heavy jest zablokowany, aby zapobiec nieautoryzowanemu dostępowi do urządzenia w dowolnym momencie.

Po zakończeniu przygotowywania urządzenia w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

![Kolejność Data Box Heavy przetworzona](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Zamówienie Data Box Heavy wysłane](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal przejdź do obszaru **Przegląd**, a następnie kliknij pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.
 
Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Data Box Heavy tematów, takich jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Data Box Heavy kolejności
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować Data Box Heavy.

> [!div class="nextstepaction"]
> [Skonfiguruj Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
