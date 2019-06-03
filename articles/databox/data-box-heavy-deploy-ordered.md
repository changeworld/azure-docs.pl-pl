---
title: Samouczek zamówienia Azure Data Box mocno | Dokumentacja firmy Microsoft
description: Dowiedz się, wymagania wstępne dotyczące wdrażania i sposób uporządkowania duże pole danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 8453a3592c1822489a3724dacdf8f0ff5e8492f1
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427915"
---
# <a name="tutorial-order-azure-data-box-heavy-preview"></a>Samouczek: Duże z zamówienia Azure Data pole (wersja zapoznawcza)


Duże pole danych platformy Azure jest hybrydowe rozwiązanie, które umożliwia importowanie danych lokalnych do platformy Azure w szybkie, łatwe i niezawodny sposób. Transfer danych na urządzenie magazynujące (przybliżony pojemności do wykorzystania) TB 770 dostarczany przez firmę Microsoft, a następnie odeślij urządzenie ponownie. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano, jak może zamówić łączność obejmującą duże pole danych platformy Azure. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące duże pole danych
> * Kolejność duże pole danych
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z usługą i urządzeniem Data Box.

### <a name="for-installation-site"></a>Dla instalacji lokacji

Przed rozpoczęciem upewnij się, że:

- Za pomocą standardowych drzwi i entryways pasuje do urządzenia. Jednak należy się upewnić, że urządzenie może składać się za pośrednictwem wszystkie swoje entryways. Wymiary urządzenia są: szerokość: 26" długość: 48" height: 28”.
- Jeśli zainstalowano piętra niż dostęp, potrzebny jest dostęp dla urządzenia za pośrednictwem elevator lub zwiększenia. Urządzenie waga wynosi około ~ 500 modułów równoważenia obciążenia.
- Upewnij się, że prostych witryn w centrum danych za pomocą odległości połączenie dostępnej sieci, która może pomieścić urządzenia przy użyciu ten system.


### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Upewnij się, że masz dostęp właściciela lub współautora do subskrypcji, aby utworzyć zamówienie duże pole danych.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:
- Urządzenie jest nieopakowanych.
- Należy posiadać komputer hosta podłączony do sieci centrum danych. Duże pole danych umożliwia skopiowanie danych z tego komputera. Komputer-host należy uruchomić obsługiwanego systemu operacyjnego, zgodnie z opisem w [Azure Data Box duże wymagania systemowe](data-box-system-requirements.md).
- Musisz mieć komputer przenośny za pomocą kabla RJ-45, aby nawiązać połączenie z lokalnego interfejsu użytkownika i skonfigurować urządzenie. Aby skonfigurować jeden raz każdego węzła urządzenia przy użyciu komputera przenośnego.
- Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE.
- Potrzebna jest jedna 40 GB/s lub 10 GB/s okablowanie na węzeł urządzenia. Wybierz kable, które są zgodne z [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) interfejs sieciowy:

    - Kabel 40 GB/s urządzenie koniec kabla musi być QSFP +.
    - Kabel 10 GB/s na potrzeby należy kabel SFP + podłączony do przełącznik 10 G, na jednym końcu QSFP + SFP + karty (lub kartą QSA) dla elementu end, które podłącza się do urządzenia.
    - Przewodów zasilania są dołączone do urządzenia.

## <a name="order-data-box-heavy"></a>Duże pole danych zamówienia

Wykonaj poniższe czynności w witrynie Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz **+ Utwórz zasób** i wyszukaj *urządzenia Azure Data Box*. Wybierz **urządzenie Azure Data Box**.
    
   [![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Wybierz pozycję **Utwórz**.

4. Sprawdź, czy usługa Data Box jest dostępne w Twoim regionie. Wprowadź lub wybierz poniższe informacje i wybierz **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Kraj źródłowy     | Wybierz kraj/region, w którym dane aktualnie znajduje się.         |
    |Docelowy region platformy Azure     | Wybierz region platformy Azure, do którego chcesz przenieść dane.        |

    [![Wybierz urządzenie Data Box rodziny dostępności](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Wybierz **Data Box ciężkich**. Maksymalnej pojemności do wykorzystania dla pojedynczego zamówienia jest 770 TB.

    [![Wybierz obciążenie pole danych](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje i wybierz **dalej**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)     | Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |
    |Grupa zasobów     | Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Docelowy region platformy Azure     | Wybierz region swojego konta magazynu. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Magazyn docelowy     | Wybierz z konta magazynu dysków zarządzanych i/lub. <br> W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. <br>Duże pole danych może być połączony z maksymalnie 10 kont magazynu. <br> Można również utworzyć nową **General-purpose v1**, **ogólnego przeznaczenia v2**, lub **konta magazynu obiektów Blob**. <br> Usługa Azure Data Lake Storage Gen 2 konta nie są obsługiwane. Zobacz [kont magazynu obsługiwanych urządzeń z systemem](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby usługa Data Box do pracy z kontami magazynu zabezpieczone, Włącz zaufanych usług w ramach ustawienia zapory sieci konta magazynu. Aby uzyskać więcej informacji, zobacz instrukcje [usługi Dodaj urządzenia Azure Data Box jako zaufaną usługę](../storage/common/storage-network-security.md#exceptions).|

    Jeśli jako miejsce docelowe przechowywania, przy użyciu konta magazynu, zostanie wyświetlony poniższy zrzut ekranu:

    ![Kolejność duże pole danych dla konta magazynu](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Oprócz konta magazynu jako miejsce docelowe magazynu również używane są duże pole danych do utworzenia dysków zarządzanych z wirtualnych dysków twardych w środowisku lokalnym, należy podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Tworzenie nowych grup zasobów, jeśli zamierzasz utworzyć dysków zarządzanych z wirtualnych dysków twardych w środowisku lokalnym. Można użyć istniejącej grupy zasobów, tylko wtedy, gdy grupa zasobów została utworzona wcześniej podczas tworzenia duże pole danych zamówienia dla dysku zarządzanego przez usługę Data Box. <br> Określ wiele grup zasobów, rozdzielając je średnikami. Obsługiwane są maksymalnie 10 grup zasobów.|

    ![Kolejność duże pole danych dla dysku zarządzanego](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako konta magazynu przejściowego. Przekazywanie usługi Data Box, pliki VHD jako obiekty BLOB konta magazynu przejściowego przed konwersją do dysków zarządzanych i przeniesienie ich do grup zasobów. Aby uzyskać więcej informacji, zobacz [danych sprawdź, czy przekazać na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Wybierz **Sprawdź poprawność adresu**. 

    Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. Wybierz opcję **Dalej**.

8. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

9. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

10. Wybierz **kolejności**. Utworzenie zamówienia trwa kilka minut.


## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia duże pole danych, a następnie przejdź do **Przegląd** Aby wyświetlić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Jeśli urządzenie nie jest niedostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

- Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
- Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
- Generowane jest również hasło urządzenia, które pomaga odblokować urządzenie.
- Duże pole danych jest zablokowane, aby uniemożliwić nieautoryzowany dostęp do urządzenia w dowolnym momencie.

Po zakończeniu przygotowywania urządzenia w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

![Kolejność duże pole danych przetworzone](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Kolejność duże pole danych zostało wysłane](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal przejdź do obszaru **Przegląd**, a następnie kliknij pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.
 
Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono Azure Data Box mocno tematy takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Duże pole danych zamówienia
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługi duże pole danych.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi duże pole danych platformy Azure](./data-box-heavy-deploy-set-up.md)
