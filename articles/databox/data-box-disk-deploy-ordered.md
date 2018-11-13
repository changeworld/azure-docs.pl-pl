---
title: Samouczek dotyczący zamawiania usługi Microsoft Azure Data Box Disk | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć konto i zamówić usługę Azure Data Box Disk w celu importowania danych na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: fae5771afea742b2746c32e1ed6ae88c511876e6
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037961"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Samouczek: zamawianie usługi Azure Data Box Disk (wersja zapoznawcza)

Azure Data Box Disk to hybrydowe rozwiązanie w chmurze, które umożliwia importowanie danych lokalnych na platformę Azure szybko, łatwo i bez problemów. Możesz przenieść dane na dyski półprzewodnikowe (SSD) dostarczone przez firmę Microsoft i wysłać te dyski z powrotem. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania usługi Azure Data Box Disk. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Tworzenie konta usługi Data Box Disk
> * Zamawianie usługi Data Box Disk
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> - Usługa Data Box Disk jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - W okresie obowiązywania wersji zapoznawczej urządzenie Data Box Disk może być wysyłane do klientów w Stanach Zjednoczonych, Europie Zachodniej i Północnej, Kanadzie i Australii. Aby uzyskać więcej informacji, przejdź do tematu [Dostępność regionalna](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Rejestrowanie

Usługa Data Box Disk jest dostępna w wersji zapoznawczej i musisz utworzyć jej konto. Wykonaj poniższe kroki, aby utworzyć konto usługi Data Box:

1. Zaloguj się do witryny Azure Portal pod adresem [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Wybierz subskrypcję, w której chcesz włączyć wersję zapoznawczą. Odpowiedz na pytania dotyczące rozmiaru danych, kraju przechowywania danych, przedziału czasowego i częstotliwości transferu danych. Kliknij pozycję **Zarejestruj mnie!**.
3. Po utworzeniu konta i włączeniu opcji korzystania z wersji zapoznawczej można zamówić usługę Data Box Disk.

## <a name="order-data-box-disk"></a>Zamawianie usługi Data Box Disk

Wykonaj poniższe czynności w witrynie [Azure Portal](https://aka.ms/azuredataboxfromdiskdocs), aby zamówić usługę Data Box Disk.

1. W lewym górnym rogu portalu kliknij pozycję **+ Utwórz zasób** i wyszukaj pozycję *Azure Data Box*. Kliknij pozycję **Azure Data Box**.
    
   ![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kliknij pozycję **Utwórz**.

3. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje i kliknij pozycję **Zastosuj**.

    ![Wybieranie opcji Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Ustawienie|Wartość|
    |---|---|
    |Subskrypcja|Wybierz subskrypcję, w ramach której włączono usługę Data Box.<br> Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Typ transferu| Importuj na platformę Azure|
    |Kraj źródłowy | Wybierz kraj, w którym aktualnie znajdują się dane.|
    |Docelowy region platformy Azure|Wybierz region platformy Azure, do którego chcesz przenieść dane.|

  
5.  Wybierz pozycję **Data Box Disk**. Maksymalna pojemność rozwiązania w pojedynczym zamówieniu obejmującym 5 dysków to 35 TB. W przypadku większych ilości danych można utworzyć wiele zamówień.

     ![Wybieranie opcji Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje.

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|Podaj przyjazną nazwę, aby śledzić zamówienie.<br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą. |
    |Grupa zasobów| Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. |
    |Docelowy region platformy Azure| Wybierz region swojego konta magazynu.<br> Obecnie są obsługiwane konta magazynu we wszystkich regionach USA, regionach Europa Zachodnia i Europa Północna, Kanadzie i Australii. |
    |Konta magazynu|W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejące konto magazynu. <br>Można również utworzyć nowe konto ogólnego przeznaczenia w wersji 1 lub konto ogólnego przeznaczenia w wersji 2. |
    |Szacowany rozmiar danych w terabajtach (TB)| Wprowadź szacowany rozmiar w TB. <br>W zależności od rozmiaru danych firma Microsoft wysyła odpowiednią liczbę dysków SSD o pojemności 8 TB (7 TB pojemności do wykorzystania). <br>Maksymalna pojemność do wykorzystania na 5 dyskach to maksymalnie 35 TB. |
    |Klucz dostępu dla dysków| Jeśli zaznaczono opcję **Użyj niestandardowego klucza dostępu zamiast tego wygenerowanego przez platformę Azure**, podaj klucz dostępu dla dysków. <br> Podaj klucz alfanumeryczny zawierający od 12 do 32 znaków, w tym co najmniej jedną cyfrę i jeden znak specjalny. Dozwolone są następujące znaki specjalne: `@?_+`. <br> Tę opcję możesz pominąć i do odblokowywania dysków korzystać z klucza dostępu wygenerowanego przez platformę Azure.|

13. Kliknij przycisk **Dalej**. 

    ![Podawanie szczegółów zamówienia](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na karcie **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Kliknij pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. 

    ![Podawanie adresu wysyłkowego](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail. 

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

16. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

17. Kliknij pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

 
## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal w wersji zapoznawczej. Przejdź do zamówienia, a następnie przejdź do obszaru **Omówienie**, aby sprawdzić stan. Zadanie wyświetlone w portalu ma stan **Zamówione**. 

![Data Box Disk — stan Zamówione](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Jeśli dyski są niedostępne, otrzymasz powiadomienie. Jeśli dyski są dostępne, firma Microsoft identyfikuje dyski do wysłania i przygotowuje odpowiedni pakiet dysków. Podczas przygotowywania dysków są wykonywane następujące akcje:

- Dyski są szyfrowane przy użyciu szyfrowania AES-128 funkcją BitLocker.  
- Dyski są blokowane, co ma zapobiec nieautoryzowanemu dostępowi.
- W trakcie tego procesu jest generowany klucz dostępu, który odblokowuje dyski.

Po zakończeniu przygotowywania dysków w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

Firma Microsoft następnie przygotowuje i wysyła dyski za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do ich śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.



## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal w wersji zapoznawczej przejdź do obszaru **Omówienie** i kliknij pozycję **Anuluj** na pasku poleceń. 

Anulowanie jest możliwe tylko, jeśli zamówiono dyski, a zamówienie jest przetwarzane pod kątem wysyłki. Po przetworzeniu zamówienia nie można go już anulować. 

![Anulowanie zamówienia](media/data-box-disk-deploy-ordered/cancel-order1.png)

Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń. 


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Tworzenie konta usługi Data Box Disk
> * Zamawianie usługi Data Box Disk
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box Disk.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box Disk](./data-box-disk-deploy-set-up.md)


