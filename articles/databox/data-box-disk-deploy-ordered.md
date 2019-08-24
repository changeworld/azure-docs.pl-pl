---
title: Samouczek przedstawiający kolejność Azure Data Box Disk | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć konto i zamówić usługę Azure Data Box Disk w celu importowania danych na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c1626542aba71f971ad96fe9f187ce9f1c1a99f3
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012938"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Samouczek: zamawianie urządzenia Azure Data Box Disk

Azure Data Box Disk to hybrydowe rozwiązanie w chmurze, które umożliwia importowanie danych lokalnych na platformę Azure szybko, łatwo i bez problemów. Możesz przenieść dane na dyski półprzewodnikowe (SSD) dostarczone przez firmę Microsoft i wysłać te dyski z powrotem. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania usługi Azure Data Box Disk. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Zamawianie usługi Data Box Disk
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z usługą Data Box i urządzeniem Data Box Disk.

### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Sprawdź, czy masz dostęp na poziomie właściciela lub współautora do subskrypcji, aby móc utworzyć zamówienie urządzenia Data Box.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:
- Masz dostępny komputer kliencki, z którego możesz skopiować dane. Na komputerze klienckim wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Zainstalowanie innego [wymaganego oprogramowania](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) w przypadku klienta z systemem Windows.  

## <a name="order-data-box-disk"></a>Zamawianie usługi Data Box Disk

Zaloguj się do:

- Azure Portal pod tym adresem URL: https://portal.azure.com w celu uporządkowania Data Box Disk.
- Lub Portal Azure Government pod tym adresem URL: https://portal.azure.us. Aby uzyskać więcej informacji, przejdź do [obszaru łączenie z Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Wykonaj następujące kroki, aby zamówić Data Box Disk.

1. W lewym górnym rogu portalu kliknij pozycję **+ Utwórz zasób** i wyszukaj pozycję *Azure Data Box*. Kliknij pozycję **Azure Data Box**.
    
   ![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kliknij przycisk **Utwórz**.

3. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje i kliknij pozycję **Zastosuj**.

    ![Wybieranie opcji Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Ustawienie|Value|
    |---|---|
    |Subscription|Wybierz subskrypcję, w ramach której włączono usługę Data Box.<br> Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Typ transferu| Importuj na platformę Azure|
    |Kraj źródłowy | Wybierz kraj/region, w którym znajdują się obecnie dane.|
    |Docelowy region platformy Azure|Wybierz region platformy Azure, do którego chcesz przenieść dane.|

  
5.  Wybierz pozycję **Data Box Disk**. Maksymalna pojemność rozwiązania w pojedynczym zamówieniu obejmującym 5 dysków to 35 TB. W przypadku większych ilości danych można utworzyć wiele zamówień.

     ![Wybieranie opcji Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje.

    |Ustawienie|Wartość|
    |---|---|
    |Name|Podaj przyjazną nazwę, aby śledzić zamówienie.<br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą. |
    |Resource group| Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. |
    |Docelowy region platformy Azure| Wybierz region swojego konta magazynu.<br> Obecnie są obsługiwane konta magazynu we wszystkich regionach USA, regionach Europa Zachodnia i Europa Północna, Kanadzie i Australii. |
    |Szacowany rozmiar danych w terabajtach (TB)| Wprowadź szacowany rozmiar w TB. <br>W zależności od rozmiaru danych firma Microsoft wysyła odpowiednią liczbę dysków SSD o pojemności 8 TB (7 TB pojemności do wykorzystania). <br>Maksymalna pojemność do wykorzystania na 5 dyskach to maksymalnie 35 TB. |
    |Klucz dostępu dla dysków| Jeśli zaznaczono opcję **Użyj niestandardowego klucza dostępu zamiast tego wygenerowanego przez platformę Azure**, podaj klucz dostępu dla dysków. <br> Podaj klucz alfanumeryczny zawierający od 12 do 32 znaków, w tym co najmniej jedną cyfrę i jeden znak specjalny. Dozwolone są następujące znaki specjalne: `@?_+`. <br> Tę opcję możesz pominąć i do odblokowywania dysków korzystać z klucza dostępu wygenerowanego przez platformę Azure.|
    |Magazyn docelowy     | Wybierz opcję z konta magazynu lub dysków zarządzanych lub obu. <br> Na podstawie określonego regionu świadczenia usługi Azure wybierz konto magazynu z listy filtrowanej istniejącego konta magazynu. Data Box Disk można połączyć tylko z 1 kontem magazynu. <br> Możesz również utworzyć nowe konto **ogólnego przeznaczenia w wersji 1**, w **wersji 2**lub na potrzeby **magazynu obiektów BLOB**. <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze urządzenie Data Box współpracują z bezpiecznymi kontami magazynu, należy włączyć usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz jak [dodać Azure Data Box jako zaufaną usługę](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).|

    W przypadku używania konta magazynu jako miejsca docelowego magazynu zostanie wyświetlony następujący zrzut ekranu:

    ![Zamówienie Data Box Disk dla konta magazynu](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Jeśli przy użyciu Data Box Disk utworzyć dyski zarządzane na podstawie lokalnych dysków VHD, należy również podać następujące informacje:

    |Ustawienie  |Value  |
    |---------|---------|
    |Resource group     | Utwórz nową grupę zasobów, jeśli zamierzasz utworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Użyj istniejącej grupy zasobów tylko wtedy, gdy została utworzona dla Data Box Disk kolejności dla dysku zarządzanego przez usługę urządzenie Data Box. <br> Obsługiwana jest tylko jedna grupa zasobów.|

    ![Kolejność Data Box Disk dla dysku zarządzanego](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla usługi Managed disks jest używane jako tymczasowe konto magazynu. Usługa urządzenie Data Box przekazuje wirtualne dyski twarde do konta magazynu tymczasowego, a następnie konwertuje je na dyski zarządzane i przenosi do grup zasobów. Aby uzyskać więcej informacji, zobacz [Sprawdzanie przekazywania danych na platformę Azure](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

13. Kliknij przycisk **Dalej**.

    ![Podawanie szczegółów zamówienia](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na karcie **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Kliknij pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. 

    ![Podawanie adresu wysyłkowego](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail. 

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

16. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

17. Kliknij pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

 
## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia, a następnie przejdź do obszaru **Omówienie**, aby sprawdzić stan. Zadanie wyświetlone w portalu ma stan **Zamówione**.

![Data Box Disk — stan Zamówione](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Jeśli dyski są niedostępne, otrzymasz powiadomienie. Jeśli dyski są dostępne, firma Microsoft identyfikuje dyski do wysłania i przygotowuje odpowiedni pakiet dysków. Podczas przygotowywania dysków są wykonywane następujące akcje:

- Dyski są szyfrowane przy użyciu szyfrowania AES-128 funkcją BitLocker.  
- Dyski są blokowane, co ma zapobiec nieautoryzowanemu dostępowi.
- W trakcie tego procesu jest generowany klucz dostępu, który odblokowuje dyski.

Po zakończeniu przygotowywania dysków w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

Firma Microsoft następnie przygotowuje i wysyła dyski za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do ich śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal przejdź do obszaru **Przegląd**, a następnie kliknij pozycję **Anuluj** na pasku poleceń.

Anulowanie jest możliwe tylko, jeśli zamówiono dyski, a zamówienie jest przetwarzane pod kątem wysyłki. Po przetworzeniu zamówienia nie można go już anulować.

![Anulowanie zamówienia](media/data-box-disk-deploy-ordered/cancel-order1.png)

Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Zamawianie usługi Data Box Disk
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box Disk.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
